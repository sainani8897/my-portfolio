<script>
  import { onMount } from "svelte";

  // State Variables
  let currentTheme = "dracula";
  let crtEffect = true;
  let isMatrixActive = false;
  let currentDir = "/";
  let uptime = "00:00:00";
  let currentTime = new Date().toLocaleTimeString();
  let mobileView = "editor"; // 'explorer', 'editor', 'terminal'
  
  // File Explorer folders state
  let expandedFolders = {
    projects: true
  };

  // Terminal state
  let terminalHistory = [];
  let currentCommand = "";
  let commandHistory = [];
  let historyIndex = 0;
  let terminalInputRef;
  let terminalOutputRef;

  // Editor Workspace state
  let openTabs = ["welcome.sh", "about_me.txt", "skills.json"];
  let activeTab = "welcome.sh";

  // Contact form input state
  let contactName = "";
  let contactEmail = "";
  let contactPhone = "";
  let contactMessage = "";
  let formLog = [];
  let formSubmitting = false;
  let formSubmitted = false;

  // Matrix canvas reference
  let matrixCanvas;
  let animationFrameId;

  // ASCII Arts defined safely with double quote escaping
  const welcomeAscii = 
    "   _____       _             _   _      ____   _____\n" +
    "  / ____|     (_)           | | | |    / __ \\ / ____|\n" +
    " | (___   __ _ _ _ __   __ _| |_| |__ | |  | | (___  \n" +
    "  \\___ \\ / _` | | '_ \\ / _` | __| '_ \\| |  | |\\___ \\ \n" +
    "  ____) | (_| | | | | | (_| | |_| | | | |__| |____) |\n" +
    " |_____/ \\__,_|_|_| |_|\\__,_|\\__|_| |_|\\____/|_____/\n";

  const neofetchAscii = [
    "  _________________  ",
    " | []           [] | ",
    " |   SainOS v4.2   | ",
    " |                 | ",
    " |  [#] [ ] [#]    | ",
    " |_________________| ",
    "       /     \\\\      ",
    "     /________\\\\     ",
    "     \\\\________/     "
  ];

  // System Themes
  const themes = {
    matrix: {
      name: "Matrix Green",
      bg: "#040804",
      bgTerminal: "#080c08",
      border: "#173017",
      borderGlow: "0 0 8px rgba(31, 60, 31, 0.3)",
      text: "#4af626",
      textDim: "#2b8a1a",
      accent: "#8cff66",
      editorBg: "#080c08",
      sidebarBg: "#030503",
      tabActive: "#142414",
      shadow: "rgba(0, 255, 0, 0.05)"
    },
    amber: {
      name: "Amber Classic",
      bg: "#080500",
      bgTerminal: "#0d0900",
      border: "#473000",
      borderGlow: "0 0 8px rgba(92, 61, 0, 0.3)",
      text: "#ffb000",
      textDim: "#b87d00",
      accent: "#ffd266",
      editorBg: "#0d0900",
      sidebarBg: "#050300",
      tabActive: "#211600",
      shadow: "rgba(255, 176, 0, 0.05)"
    },
    dracula: {
      name: "Dracula Purple",
      bg: "#0f111a",
      bgTerminal: "#1a1b26",
      border: "#343746",
      borderGlow: "0 0 8px rgba(68, 71, 90, 0.3)",
      text: "#f8f8f2",
      textDim: "#6272a4",
      accent: "#bd93f9",
      editorBg: "#1a1b26",
      sidebarBg: "#14151f",
      tabActive: "#2c2e3e",
      shadow: "rgba(189, 147, 249, 0.05)"
    },
    cyberpunk: {
      name: "Cyberpunk Neon",
      bg: "#05000b",
      bgTerminal: "#0a0117",
      border: "#ba047a",
      borderGlow: "0 0 8px rgba(241, 7, 163, 0.3)",
      text: "#00f0ff",
      textDim: "#731f85",
      accent: "#ff007f",
      editorBg: "#0a0117",
      sidebarBg: "#030006",
      tabActive: "#1c0433",
      shadow: "rgba(241, 7, 163, 0.1)"
    },
    monokai: {
      name: "Monokai Retro",
      bg: "#0f0f0c",
      bgTerminal: "#1c1d1a",
      border: "#3a3930",
      borderGlow: "0 0 8px rgba(73, 72, 62, 0.3)",
      text: "#f8f8f2",
      textDim: "#75715e",
      accent: "#a6e22e",
      editorBg: "#1c1d1a",
      sidebarBg: "#151614",
      tabActive: "#3a3930",
      shadow: "rgba(166, 226, 46, 0.05)"
    }
  };

  // Portfolio Virtual File System
  const fileSystem = {
    "welcome.sh": {
      name: "welcome.sh",
      type: "sh",
      icon: "fas fa-terminal text-info",
      content: 
        "===========================================================\n" +
        "   _____       _             _   _      ____   _____\n" +
        "  / ____|     (_)           | | | |    / __ \\ / ____|\n" +
        " | (___   __ _ _ _ __   __ _| |_| |__ | |  | | (___  \n" +
        "  \\___ \\ / _` | | '_ \\ / _` | __| '_ \\| |  | |\\___ \\ \n" +
        "  ____) | (_| | | | | | (_| | |_| | | | |__| |____) |\n" +
        " |_____/ \\__,_|_|_| |_|\\__,_|\\__|_| |_|\\____/|_____/\n" +
        "===========================================================\n" +
        "Sainath Dekonda -- Technical Lead & Software Analyst v4.2\n" +
        "===========================================================\n\n" +
        "Welcome to my interactive terminal portfolio! \n" +
        "I have constructed a mock Operating System UI to exhibit my credentials.\n\n" +
        "[!] INTERACTION MATRIX:\n" +
        "-----------------------------------------------------------\n" +
        "1. FILE TREE (Left): Click on files to open them in the workspace.\n" +
        "2. CLI TERMINAL (Bottom): Type Unix-like commands. Click tab for autocomplete.\n" +
        "3. THEME TOGGLE: Click theme names in top bar or run: theme [name]\n" +
        "4. CRT SWITCH: Toggle standard rendering or vintage screen scanlines.\n\n" +
        "Type 'help' in the terminal or double-click files to inspect them."
    },
    "about_me.txt": {
      name: "about_me.txt",
      type: "txt",
      icon: "fas fa-file-alt text-success",
      content: 
        "===========================================================\n" +
        "               BIOGRAPHY: SAINATH DEKONDA                  \n" +
        "===========================================================\n" +
        "* Current Role: Technical Lead at Wipro\n" +
        "* Focus Areas : Data Science, Data Engineering, Backend Systems\n" +
        "* Core Tech   : Python (Flask), Node.js, Databricks, Machine Learning\n" +
        "* Cloud & Ops : AWS, Datadog, Grafana, Sentry, Docker\n" +
        "* Location    : Hyderabad, India 500076\n" +
        "* Contact     : +91 9989232079 | sainani8897@gmail.com\n" +
        "-----------------------------------------------------------\n\n" +
        "I am a critical-thinking Software Analyst and Technical Lead with proven success \n" +
        "at driving software projects to meet business and user needs. \n\n" +
        "For the past several years, I have worked across the software development lifecycle, \n" +
        "ranging from Senior Fullstack Development to Data Science and Data Engineering. \n" +
        "I currently lead technical initiatives at Wipro, designing and implementing robust \n" +
        "data pipelines and ETL processes in Databricks, and applying machine learning models.\n\n" +
        "With specialized training in Data Science (Post Graduation Diploma from IIIT Bangalore) \n" +
        "and certifications in Python and Generative AI, I bridge the gap between backend \n" +
        "microservices and intelligent data systems."
    },
    "skills.json": {
      name: "skills.json",
      type: "json",
      icon: "fas fa-file-code text-warning",
      content: JSON.stringify({
        "cloud_data": {
          "platforms": ["AWS (EC2, RDS, S3)", "Databricks"],
          "methods": ["ETL", "Machine Learning", "System Architecture Design"]
        },
        "languages_frameworks": {
          "backend": ["Python (Flask)", "Node.js (Express)", "PHP (Laravel)"],
          "frontend": ["React", "Vue"],
          "apis": ["GraphQL", "RESTful Web Services"]
        },
        "databases": {
          "sql_nosql": ["MySQL", "MongoDB", "Firebase"]
        },
        "tools_monitoring": {
          "observability": ["Datadog", "Grafana", "Sentry"],
          "iot_protocols": ["MQTT"]
        },
        "certifications": [
          "Post Graduation Diploma in Data Science (IIIT Bangalore)",
          "Certification in Generative AI (Upgrad)",
          "Certification in Python Development (Upgrad)",
          "Completed Bootcamp in Python (Upgrad)"
        ]
      }, null, 2)
    },
    "experience.log": {
      name: "experience.log",
      type: "log",
      icon: "fas fa-history text-primary",
      content: 
        "[2024-PRESENT] TECHNICAL LEAD @ WIPRO\n" +
        "-----------------------------------------------------------\n" +
        "* Leading technical initiatives as a Data Scientist and Data Engineer.\n" +
        "* Leveraging Databricks for large-scale data processing and advanced analytics.\n" +
        "* Designing and implementing robust data pipelines and ETL processes within \n" +
        "  the Databricks environment to support data-driven decision-making.\n\n" +
        "[2022-2024] SOFTWARE ANALYST @ X-CUBE LABS\n" +
        "-----------------------------------------------------------\n" +
        "* Evaluated software tools for log scaling, recommending improvements \n" +
        "  using Datadog, Grafana, and Sentry for error logging.\n" +
        "* Introduced automated testing protocols, shortening software validation \n" +
        "  timeframes by 68%.\n" +
        "* Mentored junior analysts to elevate skills and overall team performance.\n\n" +
        "[2021-2022] SR. FULLSTACK DEVELOPER @ EMBEDTECH SOLUTIONS\n" +
        "-----------------------------------------------------------\n" +
        "* Developed unit test cases for testing and automation.\n" +
        "* Oversaw automated build and deployment pipelines."
    },
    "projects/vriddhi-pos.md": {
      name: "projects/vriddhi-pos.md",
      type: "md",
      icon: "fas fa-project-diagram text-info",
      title: "Vriddhi POS",
      img: "assets/img/portfolio/safe.png",
      content: 
        "# Vriddhi POS\n" +
        "--- \n" +
        "* **Category:** Point of Sale (POS) System\n" +
        "* **Core Stack:** Python, Node.js, MySQL\n" +
        "--- \n\n" +
        "**Vriddhi POS** is a Point of Sale system designed for retail management and inventory tracking.\n\n" +
        "### Key Implementations:\n" +
        "- Developed a Point of Sale (POS) system designed for retail management and inventory tracking.\n" +
        "- Focused on seamless data integration and user-friendly interface design."
    },
    "projects/hometitle-lock.md": {
      name: "projects/hometitle-lock.md",
      type: "md",
      icon: "fas fa-project-diagram text-info",
      title: "HomeTitle Lock",
      img: "assets/img/portfolio/submarine.png",
      content: 
        "# HomeTitle Lock\n" +
        "--- \n" +
        "* **Category:** Property Protection Web App\n" +
        "* **Core Stack:** Python (Flask), GraphQL, Node.js, React.js\n" +
        "* **Link:** [HomeTitle Lock](https://www.hometitlelock.com)\n" +
        "--- \n\n" +
        "HomeTitle Lock is a property security web application.\n\n" +
        "### Key Implementations:\n" +
        "- Designed and maintained a microservice distributed system architecture.\n" +
        "- Utilized Python (Flask), GraphQL, Node.js, and React.js for seamless data synchronization."
    },
    "projects/myverkoper.md": {
      name: "projects/myverkoper.md",
      type: "md",
      icon: "fas fa-project-diagram text-info",
      title: "MyVerkoper",
      img: "assets/img/portfolio/MyVerkoperhome.png",
      content: 
        "# MyVerkoper\n" +
        "--- \n" +
        "* **Category:** B2B Marketplace (EdTech Procurement)\n" +
        "* **Core Stack:** PHP Laravel, Node.js, MySQL, Redis, AWS EC2\n" +
        "* **Link:** [MyVerkoper](https://myverkoper.com/)\n" +
        "--- \n\n" +
        "MyVerkoper connects educational institutions with manufacturers.\n\n" +
        "### Key Implementations:\n" +
        "- Integrated B2B e-commerce platform connecting schools with manufacturers.\n" +
        "- Formulated transaction workflows and secure order quotation modules."
    },
    "projects/bulltrend.md": {
      name: "projects/bulltrend.md",
      type: "md",
      icon: "fas fa-project-diagram text-info",
      title: "BullTrend",
      img: "assets/img/portfolio/bull-trendsbt-portfolio.png",
      content: 
        "# BullTrend\n" +
        "--- \n" +
        "* **Category:** Stock Analytics Mobile App\n" +
        "* **Core Stack:** Node.js, Python, MongoDB, AWS\n" +
        "--- \n\n" +
        "BullTrend delivers real-time stock trends and technical indicators.\n\n" +
        "### Key Implementations:\n" +
        "- Programmed financial application for real-time stock trends monitoring.\n" +
        "- Integrated ML-based data processing pipelines using Node.js and AWS."
    },
    "contact_me.sh": {
      name: "contact_me.sh",
      type: "sh",
      icon: "fas fa-paper-plane text-danger",
      content: 
        "#!/bin/bash\n" +
        "# Outbound Contact Gateway v1.0\n" +
        "# Target: Sainath Dekonda <sainath.dekonda@gmail.com>\n" +
        "echo \"Initializing secure mail transmitter...\"\n" +
        "# Fill in the script arguments in the editor form."
    },
    "resume.url": {
      name: "resume.url",
      type: "url",
      icon: "fas fa-file-pdf text-danger",
      url: "https://docs.google.com/document/d/1-HlZx3-BzSQz6nNhDPL77tPLImw_nQjAtWOICQR1UgE/edit?usp=sharing",
      content: 
        "[InternetShortcut]\n" +
        "URL=https://docs.google.com/document/d/1-HlZx3-BzSQz6nNhDPL77tPLImw_nQjAtWOICQR1UgE/edit?usp=sharing\n" +
        "IconIndex=0"
    }
  };

  // Helper function to resolve paths
  function resolvePath(path) {
    if (!path) return null;
    if (path.startsWith("/")) return path;
    
    if (currentDir === "/") {
      return "/" + path;
    } else {
      if (path.startsWith("..")) {
        return path.replace("..", "/").replace("//", "/");
      }
      return currentDir + "/" + path;
    }
  }

  // Helper function to find a file from the VFS
  function getFile(path) {
    let resolved = resolvePath(path);
    if (!resolved) return null;
    let key = resolved.startsWith("/") ? resolved.substring(1) : resolved;
    return fileSystem[key] || null;
  }

  // Open a file in the workspace GUI
  function openFileInTab(key) {
    const file = fileSystem[key];
    if (!file) return;

    if (file.type === "url") {
      executeCommand(`cat ${key}`, false);
      return;
    }

    if (!openTabs.includes(key)) {
      openTabs = [...openTabs, key];
    }
    activeTab = key;

    // Echo cat command to terminal history
    executeCommand(`cat ${key}`, false);

    // If mobile, focus editor pane
    mobileView = "editor";
  }

  // Close tab in editor
  function closeTab(key, event) {
    event.stopPropagation();
    openTabs = openTabs.filter(t => t !== key);
    if (activeTab === key) {
      activeTab = openTabs.length > 0 ? openTabs[openTabs.length - 1] : "";
    }
  }

  // Execute terminal CLI command
  function executeCommand(cmdStr, silentInput = false) {
    const trimmed = cmdStr.trim();
    if (!trimmed) return;

    // Add to history records
    if (!silentInput) {
      commandHistory = [...commandHistory, trimmed];
      historyIndex = commandHistory.length;
      terminalHistory = [...terminalHistory, { text: `guest@sainath-os:${currentDir}$ ${trimmed}`, type: "input" }];
    }

    const parts = trimmed.split(" ");
    const cmd = parts[0].toLowerCase();
    const args = parts.slice(1);

    switch (cmd) {
      case "clear":
        terminalHistory = [];
        break;

      case "help":
      case "?":
        printHelp();
        break;

      case "ls":
        printLs();
        break;

      case "cd":
        printCd(args[0]);
        break;

      case "cat":
        printCat(args[0]);
        break;

      case "skills":
        printSkills();
        break;

      case "projects":
        printProjects();
        break;

      case "project":
        printProjectDetail(args[0]);
        break;

      case "contact":
        printContact();
        break;

      case "resume":
        printResume();
        break;

      case "socials":
        printSocials();
        break;

      case "neofetch":
      case "sysinfo":
        printNeofetch();
        break;

      case "matrix":
        isMatrixActive = true;
        setTimeout(startMatrixRain, 50);
        terminalHistory = [...terminalHistory, { text: "Starting Matrix code rain. Press ESC or click screen to close.", type: "success" }];
        break;

      case "theme":
        printTheme(args[0]);
        break;

      case "whoami":
        terminalHistory = [...terminalHistory, { text: "guest (authorized user)", type: "output" }];
        break;

      case "date":
        terminalHistory = [...terminalHistory, { text: new Date().toString(), type: "output" }];
        break;

      case "sudo":
        terminalHistory = [...terminalHistory, { text: "Error: Nice try! Permission denied. System self-defense active.", type: "error" }];
        break;

      default:
        terminalHistory = [...terminalHistory, { text: `Command not found: ${cmd}. Type 'help' for options.`, type: "error" }];
    }

    currentCommand = "";
    // Scroll terminal
    setTimeout(scrollToBottom, 20);
  }

  // Autocomplete command matching
  function autoCompleteCommand() {
    const val = currentCommand.trim().toLowerCase();
    if (!val) return;
    
    const commands = ["help", "ls", "cd", "cat", "clear", "neofetch", "sysinfo", "skills", "projects", "project", "contact", "resume", "matrix", "theme", "socials", "whoami", "date"];
    const files = ["about_me.txt", "skills.json", "experience.log", "contact_me.sh", "resume.url", "projects/vriddhi-pos.md", "projects/hometitle-lock.md", "projects/myverkoper.md", "projects/bulltrend.md"];

    if (val.startsWith("cat ")) {
      const search = val.substring(4);
      const matches = files.filter(f => f.startsWith(search) || f.split("/")[1]?.startsWith(search));
      if (matches.length === 1) {
        currentCommand = `cat ${matches[0]}`;
      } else if (matches.length > 1) {
        terminalHistory = [...terminalHistory, 
          { text: `guest@sainath-os:${currentDir}$ ${currentCommand}`, type: "input" },
          { text: matches.join("   "), type: "info" }
        ];
      }
    } else if (val.startsWith("theme ")) {
      const search = val.substring(6);
      const matches = Object.keys(themes).filter(t => t.startsWith(search));
      if (matches.length === 1) {
        currentCommand = `theme ${matches[0]}`;
      } else if (matches.length > 1) {
        terminalHistory = [...terminalHistory, 
          { text: `guest@sainath-os:${currentDir}$ ${currentCommand}`, type: "input" },
          { text: matches.join("   "), type: "info" }
        ];
      }
    } else if (val.startsWith("project ")) {
      const search = val.substring(8);
      const projs = ["vriddhi-pos", "hometitle-lock", "myverkoper", "bulltrend"];
      const matches = projs.filter(p => p.startsWith(search));
      if (matches.length === 1) {
        currentCommand = `project ${matches[0]}`;
      } else if (matches.length > 1) {
        terminalHistory = [...terminalHistory, 
          { text: `guest@sainath-os:${currentDir}$ ${currentCommand}`, type: "input" },
          { text: matches.join("   "), type: "info" }
        ];
      }
    } else {
      const matches = commands.filter(c => c.startsWith(val));
      if (matches.length === 1) {
        currentCommand = matches[0];
      } else if (matches.length > 1) {
        terminalHistory = [...terminalHistory, 
          { text: `guest@sainath-os:${currentDir}$ ${currentCommand}`, type: "input" },
          { text: matches.join("   "), type: "info" }
        ];
      }
    }
  }

  // Keyboard controls
  function handleKeyDown(e) {
    if (e.key === "Tab") {
      e.preventDefault();
      autoCompleteCommand();
    } else if (e.key === "ArrowUp") {
      e.preventDefault();
      if (commandHistory.length > 0 && historyIndex > 0) {
        historyIndex--;
        currentCommand = commandHistory[historyIndex];
      }
    } else if (e.key === "ArrowDown") {
      e.preventDefault();
      if (commandHistory.length > 0) {
        if (historyIndex < commandHistory.length - 1) {
          historyIndex++;
          currentCommand = commandHistory[historyIndex];
        } else {
          historyIndex = commandHistory.length;
          currentCommand = "";
        }
      }
    } else if (e.key === "Escape" && isMatrixActive) {
      stopMatrixRain();
    }
  }

  // Help command printer
  function printHelp() {
    const helpLines = [
      "===========================================================",
      "Available Commands:",
      "  help, ?          Show this manual",
      "  ls               List files in active directory",
      "  cd [dir]         Change directory (e.g. cd projects, cd ..)",
      "  cat [file]       Render file contents (also opens code tab)",
      "  neofetch         Print system logs and portrait ASCII",
      "  skills           List all technical capabilities in a grid",
      "  projects         Show short summaries of all client projects",
      "  project [name]   Detailed overview of specific portfolio item",
      "  contact          Display location & media accounts info",
      "  resume           Open Google Docs Resume in new tab",
      "  socials          List social links (GitHub, LinkedIn, Twitter)",
      "  theme [name]     Modify color palette (matrix, amber, dracula, cyberpunk, monokai)",
      "  matrix           Trigger falling matrix screensaver (ESC to close)",
      "  clear            Reset console records",
      "==========================================================="
    ];
    terminalHistory = [...terminalHistory, ...helpLines.map(l => ({ text: l, type: "output" }))];
  }

  // List directory printer
  function printLs() {
    if (currentDir === "/") {
      terminalHistory = [
        ...terminalHistory,
        { text: "about_me.txt   skills.json   experience.log   contact_me.sh   resume.url", type: "output" },
        { text: "projects/ (directory)", type: "info" }
      ];
    } else if (currentDir === "/projects") {
      terminalHistory = [
        ...terminalHistory,
        { text: "vriddhi-pos.md   hometitle-lock.md   myverkoper.md   bulltrend.md", type: "output" }
      ];
    }
  }

  // Change directory printer
  function printCd(dir) {
    if (!dir || dir === "." || dir === "/") {
      currentDir = "/";
    } else if (dir === "..") {
      currentDir = "/";
    } else if (dir === "projects" || dir === "/projects") {
      currentDir = "/projects";
    } else {
      terminalHistory = [...terminalHistory, { text: `cd: no such directory: ${dir}`, type: "error" }];
    }
  }

  // File printer
  function printCat(filename) {
    if (!filename) {
      terminalHistory = [...terminalHistory, { text: "Usage: cat [filename]", type: "error" }];
      return;
    }
    const file = getFile(filename);
    if (!file) {
      terminalHistory = [...terminalHistory, { text: `cat: file not found: ${filename}`, type: "error" }];
      return;
    }

    if (file.type === "url") {
      terminalHistory = [...terminalHistory, { text: `Redirecting to external link: ${file.url}`, type: "success" }];
      window.open(file.url, "_blank");
      return;
    }

    // If tab is closed, reopen it
    if (!openTabs.includes(file.name)) {
      openTabs = [...openTabs, file.name];
    }
    activeTab = file.name;

    const lines = file.content.split("\n");
    terminalHistory = [...terminalHistory, ...lines.map(l => ({ text: l, type: "output" }))];
  }

  // Skills printer
  function printSkills() {
    if (!openTabs.includes("skills.json")) {
      openTabs = [...openTabs, "skills.json"];
    }
    activeTab = "skills.json";
    
    const lines = [
      "===========================================================",
      "                     CORE CAPABILITIES                     ",
      "===========================================================",
      " [ CLOUD & DATA ] AWS (EC2/RDS/S3), Databricks, ETL Processes,",
      "                  Machine Learning, System Architecture Design",
      " [ LANGUAGES ]    Python (Flask), Node.js (Express), PHP (Laravel)",
      "                  React, Vue.js, GraphQL, RESTful Services",
      " [ DATABASES ]    MySQL, MongoDB, Firebase",
      " [ MONITORING ]   Datadog, Grafana, Sentry, MQTT Protocol",
      " [ TRAINING ]     IIIT Bangalore Post Graduation Diploma (Data Science)",
      "==========================================================="
    ];
    terminalHistory = [...terminalHistory, ...lines.map(l => ({ text: l, type: "output" }))];
  }

  // Projects printer
  function printProjects() {
    const lines = [
      "===========================================================",
      "                      PORTFOLIO OVERVIEW                   ",
      "===========================================================",
      " 1. VRIDDHI POS       - Point of Sale management system for retail",
      " 2. HOMETITLE LOCK    - Distributed microservices property protection",
      " 3. MYVERKOPER        - B2B educational procurement marketplace",
      " 4. BULLTREND         - Stock analytics mobile application & ML",
      "-----------------------------------------------------------",
      " Run: project [name/index] to view, or check explorer tabs."
    ];
    terminalHistory = [...terminalHistory, ...lines.map(l => ({ text: l, type: "output" }))];
  }

  // Project details printer
  function printProjectDetail(proj) {
    if (!proj) {
      terminalHistory = [...terminalHistory, { text: "Usage: project [name-or-index]", type: "error" }];
      return;
    }
    const mapping = {
      "1": "projects/vriddhi-pos.md",
      "vriddhi": "projects/vriddhi-pos.md",
      "vriddhi-pos": "projects/vriddhi-pos.md",
      "2": "projects/hometitle-lock.md",
      "hometitle": "projects/hometitle-lock.md",
      "hometitle-lock": "projects/hometitle-lock.md",
      "3": "projects/myverkoper.md",
      "myverkoper": "projects/myverkoper.md",
      "4": "projects/bulltrend.md",
      "bull": "projects/bulltrend.md",
      "bulltrend": "projects/bulltrend.md"
    };

    const targetKey = mapping[proj.toLowerCase()];
    if (targetKey && fileSystem[targetKey]) {
      if (!openTabs.includes(targetKey)) {
        openTabs = [...openTabs, targetKey];
      }
      activeTab = targetKey;
      const lines = fileSystem[targetKey].content.split("\n");
      terminalHistory = [...terminalHistory, ...lines.map(l => ({ text: l, type: "output" }))];
    } else {
      terminalHistory = [...terminalHistory, { text: `Project not found: ${proj}. Run 'projects' for list.`, type: "error" }];
    }
  }

  // Contact info printer
  function printContact() {
    if (!openTabs.includes("contact_me.sh")) {
      openTabs = [...openTabs, "contact_me.sh"];
    }
    activeTab = "contact_me.sh";

    const lines = [
      "===========================================================",
      "                     CONTACT DIRECTORY                     ",
      "===========================================================",
      " * Name     : Sainath Dekonda",
      " * Location : Nacharam, Hyderabad, Telangana, India - 500076",
      " * Email    : sainath.dekonda@gmail.com",
      " * Resumé   : Run 'resume' or click resume.url",
      "-----------------------------------------------------------",
      " Double-click 'contact_me.sh' to execute the mailing wizard."
    ];
    terminalHistory = [...terminalHistory, ...lines.map(l => ({ text: l, type: "output" }))];
  }

  // Resume printer
  function printResume() {
    terminalHistory = [...terminalHistory, { text: "Opening Sainath's resume in a secure browser window...", type: "success" }];
    window.open("https://docs.google.com/document/d/1-HlZx3-BzSQz6nNhDPL77tPLImw_nQjAtWOICQR1UgE/edit?usp=sharing", "_blank");
  }

  // Social links printer
  function printSocials() {
    const lines = [
      "===========================================================",
      "                      SOCIAL PROFILES                      ",
      "===========================================================",
      " * LinkedIn : https://www.linkedin.com/in/sainath-dekonda-b3ba91131/",
      " * GitHub   : https://github.com/sainani8897",
      " * Twitter  : https://twitter.com/SainathDekonda",
      " * Instagram: https://www.instagram.com/sainath2009/",
      "==========================================================="
    ];
    terminalHistory = [...terminalHistory, ...lines.map(l => ({ text: l, type: "output" }))];
  }

  // Neofetch printer
  function printNeofetch() {
    const specs = [
      "guest@sainath-desktop",
      "---------------------",
      "OS: SainOS v4.2-LTS-Core (Svelte)",
      `Uptime: ${uptime}`,
      "Shell: sain-zsh v2.1",
      `Theme: ${themes[currentTheme].name}`,
      "Terminal: Svelte XTerm 3.0",
      "CPU: Databricks Spark Analytics Node",
      "RAM: Virtualized 2048MB Heap",
      "Author: Sainath Dekonda",
      "Expertise: Technical Lead / Software Analyst"
    ];

    let merged = [];
    const maxLen = Math.max(neofetchAscii.length, specs.length);
    for (let i = 0; i < maxLen; i++) {
      const artCol = (neofetchAscii[i] || "").padEnd(23);
      const textCol = specs[i] || "";
      merged.push(artCol + textCol);
    }

    terminalHistory = [...terminalHistory, ...merged.map(l => ({ text: l, type: "output" }))];
  }

  // Theme changer printer
  function printTheme(name) {
    if (!name) {
      terminalHistory = [
        ...terminalHistory,
        { text: "Usage: theme [theme-name]", type: "info" },
        { text: `Available themes: ${Object.keys(themes).join(", ")}`, type: "info" },
        { text: `Current theme: ${currentTheme}`, type: "info" }
      ];
      return;
    }
    if (themes[name.toLowerCase()]) {
      currentTheme = name.toLowerCase();
      terminalHistory = [...terminalHistory, { text: `Theme shifted to: ${themes[currentTheme].name}`, type: "success" }];
    } else {
      terminalHistory = [...terminalHistory, { text: `Unknown theme: ${name}. Available: ${Object.keys(themes).join(", ")}`, type: "error" }];
    }
  }

  // Helper: auto-scroller for terminal
  function scrollToBottom() {
    if (terminalOutputRef) {
      terminalOutputRef.scrollTop = terminalOutputRef.scrollHeight;
    }
  }

  // Helper: focuses the terminal command input
  function focusTerminal() {
    if (terminalInputRef) {
      terminalInputRef.focus();
    }
  }

  // Syntax highlighting for JSON display
  function formatJSON(jsonStr) {
    return jsonStr
      .replace(/&/g, "&amp;")
      .replace(/</g, "&lt;")
      .replace(/>/g, "&gt;")
      .replace(/("(\\u[a-zA-Z0-9]{4}|\\[^u]|[^\\"])*"(\s*:)?|\b(true|false|null)\b|-?\d+(?:\.\d*)?(?:[eE][+-]?\d+)?)/g, function (match) {
        let cls = "number";
        if (/^"/.test(match)) {
          if (/:$/.test(match)) {
            cls = "key";
          } else {
            cls = "string";
          }
        } else if (/true|false/.test(match)) {
          cls = "boolean";
        } else if (/null/.test(match)) {
          cls = "null";
        }
        return `<span class="json-${cls}">${match}</span>`;
      });
  }

  // Syntax highlighting for terminal scripts
  function formatShell(shStr) {
    return shStr
      .split("\n")
      .map(line => {
        if (line.startsWith("#")) return `<span class="sh-comment">${line}</span>`;
        if (line.startsWith("echo")) return `<span class="sh-cmd">echo</span> <span class="sh-string">${line.substring(4)}</span>`;
        return line;
      })
      .join("\n");
  }

  // Contact submit simulator
  function submitContactForm() {
    if (!contactName || !contactEmail || !contactMessage) return;
    formSubmitting = true;
    formLog = [];

    const steps = [
      { text: "Pinging smtp.sainath.dev mail gateway...", status: "WAIT", type: "info" },
      { text: "Handshake: Connection secured at port 465 (SSL).", status: " OK ", type: "success" },
      { text: "Verifying sender signature...", status: "WAIT", type: "info" },
      { text: "Sender validation: COMPLETED.", status: " OK ", type: "success" },
      { text: "Encrypting parameters with client key...", status: "WAIT", type: "info" },
      { text: "Digest: AES-256 (32-byte block checksum).", status: " OK ", type: "success" },
      { text: "Pushing payload packets into server queues...", status: "WAIT", type: "info" },
      { text: "Transmission success. Mail delivery confirmed.", status: " OK ", type: "success" }
    ];

    let i = 0;
    function logNext() {
      if (i < steps.length) {
        formLog = [...formLog, steps[i]];
        i++;
        setTimeout(logNext, 500);
      } else {
        formSubmitting = false;
        formSubmitted = true;
        
        // Echo transmission to terminal
        terminalHistory = [
          ...terminalHistory,
          { text: `[system] Success: Message from ${contactName} successfully transmitted.`, type: "success" }
        ];
        
        // Clear fields
        contactName = "";
        contactPhone = "";
        contactMessage = "";
        setTimeout(scrollToBottom, 20);
      }
    }
    logNext();
  }

  // Reset contact form state
  function resetContactForm() {
    formSubmitted = false;
    formSubmitting = false;
    formLog = [];
  }

  // Matrix screensaver loop
  function startMatrixRain() {
    if (!matrixCanvas) return;
    const ctx = matrixCanvas.getContext("2d");
    matrixCanvas.width = window.innerWidth;
    matrixCanvas.height = window.innerHeight;

    const columns = Math.floor(matrixCanvas.width / 20) + 1;
    const yPositions = Array(columns).fill(0);

    ctx.fillStyle = "#000";
    ctx.fillRect(0, 0, matrixCanvas.width, matrixCanvas.height);

    function draw() {
      ctx.fillStyle = "rgba(0, 0, 0, 0.05)";
      ctx.fillRect(0, 0, matrixCanvas.width, matrixCanvas.height);

      ctx.fillStyle = themes[currentTheme].text || "#4af626";
      ctx.font = "16px monospace";

      yPositions.forEach((y, index) => {
        const text = String.fromCharCode(Math.random() * 96 + 33);
        const x = index * 20;
        ctx.fillText(text, x, y);

        if (y > 100 + Math.random() * 10000) {
          yPositions[index] = 0;
        } else {
          yPositions[index] = y + 20;
        }
      });
      animationFrameId = requestAnimationFrame(draw);
    }
    
    cancelAnimationFrame(animationFrameId);
    draw();
  }

  // Stop Matrix screensaver
  function stopMatrixRain() {
    cancelAnimationFrame(animationFrameId);
    isMatrixActive = false;
  }

  // Setup timers on mount
  onMount(() => {
    // Session uptime counter
    const startTime = Date.now();
    const uptimeTimer = setInterval(() => {
      const diff = Date.now() - startTime;
      const hrs = Math.floor(diff / 3600000).toString().padStart(2, "0");
      const mins = Math.floor((diff % 3600000) / 60000).toString().padStart(2, "0");
      const secs = Math.floor((diff % 60000) / 1000).toString().padStart(2, "0");
      uptime = `${hrs}:${mins}:${secs}`;
    }, 1000);

    // Dynamic clock
    const clockTimer = setInterval(() => {
      currentTime = new Date().toLocaleTimeString();
    }, 1000);

    // Initial terminal greeting and welcome script load
    terminalHistory = [
      { text: "System Boot Initialized...", type: "system" },
      { text: "Mounting VFS (Virtual File System) maps... Done.", type: "system" },
      { text: "Connecting to database node @ Nacharam... Linked.", type: "system" },
      { text: "Decrypting environment blocks...", type: "system" },
      { text: "Boot completed successfully.", type: "success" },
      { text: "-----------------------------------------------------------", type: "info" }
    ];
    
    // Execute cat welcome.sh silently to load UI
    executeCommand("cat welcome.sh", true);

    // Auto-focus terminal inputs
    focusTerminal();

    // Window resize handler for Matrix canvas
    const handleResize = () => {
      if (isMatrixActive && matrixCanvas) {
        matrixCanvas.width = window.innerWidth;
        matrixCanvas.height = window.innerHeight;
      }
    };
    window.addEventListener("resize", handleResize);

    return () => {
      clearInterval(uptimeTimer);
      clearInterval(clockTimer);
      cancelAnimationFrame(animationFrameId);
      window.removeEventListener("resize", handleResize);
    };
  });
</script>

<!-- Global CSS Overwrites -->
<svelte:body on:keydown={handleKeyDown} />

<!-- Main Desktop wrapper -->
<main 
  class="terminal-os {crtEffect ? 'crt-effect' : ''}" 
  style="
    --bg: {themes[currentTheme].bg};
    --bg-terminal: {themes[currentTheme].bgTerminal};
    --border: {themes[currentTheme].border};
    --border-glow: {themes[currentTheme].borderGlow};
    --text: {themes[currentTheme].text};
    --text-dim: {themes[currentTheme].textDim};
    --accent: {themes[currentTheme].accent};
    --editor-bg: {themes[currentTheme].editorBg};
    --sidebar-bg: {themes[currentTheme].sidebarBg};
    --tab-active: {themes[currentTheme].tabActive};
    --shadow: {themes[currentTheme].shadow};
  "
>
  <!-- TOP OS BAR -->
  <div class="top-bar">
    <div class="system-brand">
      <span class="pulse-dot"></span>
      <span class="brand-text">SainOS v4.2-LTS</span>
    </div>

    <!-- Active Theme / Quick switcher -->
    <div class="theme-quick-selector">
      <span class="meta-label">THEME:</span>
      {#each Object.keys(themes) as key}
        <button 
          class="theme-btn {currentTheme === key ? 'active' : ''}"
          on:click={() => executeCommand(`theme ${key}`)}
        >
          {key.toUpperCase()}
        </button>
      {/each}
    </div>

    <!-- CRT Toggler & System clock -->
    <div class="top-bar-meta">
      <button 
        class="crt-toggle-btn {crtEffect ? 'active' : ''}"
        on:click={() => crtEffect = !crtEffect}
      >
        <i class="fas fa-tv"></i> CRT: {crtEffect ? "ON" : "OFF"}
      </button>
      <span class="meta-item"><i class="fas fa-hourglass-start"></i> UP: {uptime}</span>
      <span class="meta-item clock"><i class="far fa-clock"></i> {currentTime}</span>
    </div>
  </div>

  <!-- MAIN ENVIRONMENT CONTAINER -->
  <div class="desktop-workspace">
    
    <!-- LEFT SIDEBAR: FILE EXPLORER -->
    <aside class="sidebar-panel {mobileView === 'explorer' ? 'mobile-show' : 'mobile-hide'}">
      <div class="panel-header">
        <span class="header-title"><i class="fas fa-folder-tree"></i> FILE EXPLORER</span>
        <div class="window-actions">
          <span class="dot close"></span>
          <span class="dot minimize"></span>
          <span class="dot maximize"></span>
        </div>
      </div>

      <!-- PROFILE IMAGE CARD -->
      <div class="sidebar-profile-card">
        <img src="assets/img/20211217_183735_ccexpress.png" class="profile-avatar" alt="Sainath Dekonda" />
        <div class="profile-info">
          <h3 class="profile-name">Sainath Dekonda</h3>
          <span class="profile-title">Software Analyst / Tech Lead</span>
        </div>
      </div>
      
      <div class="explorer-content">
        <div class="vfs-root">
          <span class="root-name"><i class="fas fa-network-wired"></i> sainath@desktop:~/</span>
          
          <div class="tree-items">
            <!-- welcome.sh -->
            <button 
              class="tree-item file {activeTab === 'welcome.sh' ? 'active' : ''}" 
              on:click={() => openFileInTab('welcome.sh')}
            >
              <i class="fas fa-terminal text-info"></i>
              <span class="item-label">welcome.sh</span>
            </button>
            
            <!-- about_me.txt -->
            <button 
              class="tree-item file {activeTab === 'about_me.txt' ? 'active' : ''}" 
              on:click={() => openFileInTab('about_me.txt')}
            >
              <i class="fas fa-file-invoice text-success"></i>
              <span class="item-label">about_me.txt</span>
            </button>
            
            <!-- skills.json -->
            <button 
              class="tree-item file {activeTab === 'skills.json' ? 'active' : ''}" 
              on:click={() => openFileInTab('skills.json')}
            >
              <i class="fas fa-file-code text-warning"></i>
              <span class="item-label">skills.json</span>
            </button>
            
            <!-- experience.log -->
            <button 
              class="tree-item file {activeTab === 'experience.log' ? 'active' : ''}" 
              on:click={() => openFileInTab('experience.log')}
            >
              <i class="fas fa-file-signature text-primary"></i>
              <span class="item-label">experience.log</span>
            </button>

            <!-- Folder: projects -->
            <div class="tree-folder">
              <button 
                class="tree-item folder-trigger" 
                on:click={() => expandedFolders.projects = !expandedFolders.projects}
              >
                <i class="fas {expandedFolders.projects ? 'fa-folder-open' : 'fa-folder'} text-warning"></i>
                <span class="item-label">projects/</span>
              </button>
              
              {#if expandedFolders.projects}
                <div class="folder-children">
                  {#each Object.keys(fileSystem) as key}
                    {#if key.startsWith("projects/")}
                      <button 
                        class="tree-item file child-item {activeTab === key ? 'active' : ''}" 
                        on:click={() => openFileInTab(key)}
                      >
                        <i class="fas fa-file-code text-info"></i>
                        <span class="item-label">{key.split("/")[1]}</span>
                      </button>
                    {/if}
                  {/each}
                </div>
              {/if}
            </div>

            <!-- contact_me.sh -->
            <button 
              class="tree-item file {activeTab === 'contact_me.sh' ? 'active' : ''}" 
              on:click={() => openFileInTab('contact_me.sh')}
            >
              <i class="fas fa-paper-plane text-danger"></i>
              <span class="item-label">contact_me.sh</span>
            </button>

            <!-- resume.url -->
            <button 
              class="tree-item file" 
              on:click={() => openFileInTab('resume.url')}
            >
              <i class="fas fa-file-pdf text-danger"></i>
              <span class="item-label">resume.url</span>
            </button>
          </div>
        </div>
      </div>
    </aside>

    <!-- RIGHT CONTENT: TABBED EDITOR & TERMINAL -->
    <div class="workspace-contents">
      
      <!-- TOP PANEL: TABBED EDITOR -->
      <section class="editor-panel {mobileView === 'editor' ? 'mobile-show' : 'mobile-hide'}">
        <div class="editor-header">
          <!-- Editor Tabs -->
          <div class="editor-tabs">
            {#each openTabs as tab}
              <button 
                class="tab-item {activeTab === tab ? 'active' : ''}" 
                on:click={() => activeTab = tab}
              >
                <i class="{fileSystem[tab]?.icon || 'fas fa-file-code'} tab-file-icon"></i>
                <span class="tab-label">{tab}</span>
                <span 
                  class="tab-close-btn" 
                  role="button" 
                  tabindex="0"
                  on:click|stopPropagation={(e) => closeTab(tab, e)}
                  on:keydown={() => {}}
                >
                  <i class="fas fa-times"></i>
                </span>
              </button>
            {/each}
            {#if openTabs.length === 0}
              <span class="no-tabs-label">No active files</span>
            {/if}
          </div>

          <div class="window-actions">
            <span class="dot close"></span>
            <span class="dot minimize"></span>
            <span class="dot maximize"></span>
          </div>
        </div>

        <!-- Editor active screen -->
        <div class="editor-body">
          {#if activeTab && fileSystem[activeTab]}
            <!-- LINE NUMBERS -->
            <div class="line-numbers-col">
              {#each Array(fileSystem[activeTab].content.split("\n").length) as _, index}
                <span class="line-num">{index + 1}</span>
              {/each}
              {#if activeTab === "contact_me.sh" && !formSubmitted && !formSubmitting}
                <!-- extra lines for contact form -->
                <span class="line-num">{fileSystem[activeTab].content.split("\n").length + 1}</span>
                <span class="line-num">{fileSystem[activeTab].content.split("\n").length + 2}</span>
                <span class="line-num">{fileSystem[activeTab].content.split("\n").length + 3}</span>
                <span class="line-num">{fileSystem[activeTab].content.split("\n").length + 4}</span>
                <span class="line-num">{fileSystem[activeTab].content.split("\n").length + 5}</span>
              {/if}
            </div>

            <!-- CODE CONTENT RENDERER -->
            <div class="code-view-col">
              {#if fileSystem[activeTab].type === "sh" && activeTab !== "contact_me.sh"}
                <pre class="code-block">{@html formatShell(fileSystem[activeTab].content)}</pre>
              
              {:else if fileSystem[activeTab].type === "json"}
                <pre class="code-block">{@html formatJSON(fileSystem[activeTab].content)}</pre>
              
              {:else if activeTab === "about_me.txt"}
                <!-- Render profile and bio text side by side -->
                <div class="about-rendered-view">
                  <div class="about-grid">
                    <div class="about-avatar-column">
                      <div class="avatar-frame">
                        <img src="assets/img/20211217_183735_ccexpress.png" class="about-avatar-img" alt="Sainath Dekonda" />
                        <div class="frame-overlay"></div>
                      </div>
                      <div class="system-badge">STATUS: ACTIVE</div>
                    </div>
                    
                    <div class="about-text-column">
                      <pre class="code-block plain-text">{fileSystem[activeTab].content}</pre>
                    </div>
                  </div>
                </div>

              {:else if fileSystem[activeTab].type === "txt" || fileSystem[activeTab].type === "log"}
                <pre class="code-block plain-text">{fileSystem[activeTab].content}</pre>
              
              {:else if fileSystem[activeTab].type === "md"}
                <!-- Render project template card -->
                <div class="markdown-rendered-view">
                  <div class="project-header-panel">
                    <span class="project-label">PROJECT SPECIFICATION</span>
                    <h2 class="project-title">{fileSystem[activeTab].title}</h2>
                    <p class="project-tags">
                      <span class="tag-title">STACK:</span> 
                      <span class="tag-content">{fileSystem[activeTab].content.match(/\*Stack:\*(.*)/)?.[1] || "Fullstack"}</span>
                    </p>
                  </div>
                  
                  {#if fileSystem[activeTab].img}
                    <div class="project-img-frame">
                      <img 
                        src={fileSystem[activeTab].img} 
                        class="project-img" 
                        alt="{fileSystem[activeTab].title} Screenshot" 
                      />
                      <div class="frame-scanlines"></div>
                    </div>
                  {/if}

                  <div class="project-markdown-content">
                    <!-- Parse out description -->
                    <p class="description-text">
                      {fileSystem[activeTab].content.split("---")[2]?.replace(/### Key Implementations:\s*/, "")?.replace(/^- /gm, "• ") || fileSystem[activeTab].content}
                    </p>
                  </div>
                </div>

              {:else if activeTab === "contact_me.sh"}
                <!-- Contact script form -->
                <div class="contact-script-form">
                  <pre class="code-block">{@html formatShell(fileSystem[activeTab].content)}</pre>
                  
                  {#if !formSubmitting && !formSubmitted}
                    <form class="shell-fields" on:submit|preventDefault={submitContactForm}>
                      <div class="field-row">
                        <label for="nameInput" class="field-prompt">guest@sainath-os:~$ read -p "Enter your Name: "</label>
                        <input id="nameInput" type="text" class="field-input" bind:value={contactName} required placeholder="Full Name..." />
                      </div>
                      <div class="field-row">
                        <label for="emailInput" class="field-prompt">guest@sainath-os:~$ read -p "Enter your Email: "</label>
                        <input id="emailInput" type="email" class="field-input" bind:value={contactEmail} required placeholder="email@example.com..." />
                      </div>
                      <div class="field-row">
                        <label for="phoneInput" class="field-prompt">guest@sainath-os:~$ read -p "Enter your Phone: "</label>
                        <input id="phoneInput" type="tel" class="field-input" bind:value={contactPhone} placeholder="Phone number..." />
                      </div>
                      <div class="field-row message-field">
                        <label for="messageInput" class="field-prompt">guest@sainath-os:~$ read -r -p "Enter Message: "</label>
                        <textarea id="messageInput" class="field-textarea" bind:value={contactMessage} required placeholder="Write message details..."></textarea>
                      </div>
                      <div class="action-row">
                        <button type="submit" class="submit-sh-btn">[ RUN MESSAGE_DAEMON.SH ]</button>
                      </div>
                    </form>

                  {:else if formSubmitting}
                    <div class="transmission-logs">
                      {#each formLog as log}
                        <div class="log-line {log.type}">
                          <span class="log-status">[{log.status}]</span>
                          <span class="log-text">{log.text}</span>
                        </div>
                      {/each}
                      <div class="blink-cursor-inline"></div>
                    </div>

                  {:else if formSubmitted}
                    <div class="transmission-success">
                      <div class="terminal-card-success">
                        <div class="card-border"></div>
                        <h4 class="card-title"><i class="fas fa-check-circle"></i> TRANSMISSION RECEIVED SUCCESSFULLY</h4>
                        <p class="card-msg">Your message has been encrypted and securely forwarded onto Sainath's primary server inbox.</p>
                        <div class="card-meta-info">
                          <span>SMTP_STATUS: 200 OK</span>
                          <span>HOST: sainath-server-production</span>
                          <span>SSL: ACTIVE</span>
                        </div>
                        <button class="reset-sh-btn" on:click={resetContactForm}>[ SEND ANOTHER MESSAGE ]</button>
                      </div>
                    </div>
                  {/if}
                </div>
              {/if}
            </div>
          {:else}
            <div class="empty-editor-splash">
              <i class="fas fa-code splash-icon"></i>
              <h3>SAINOS WORKSPACE EDITOR</h3>
              <p>Select a file from the explorer sidebar or type 'help' in the terminal to begin.</p>
            </div>
          {/if}
        </div>
      </section>

      <!-- BOTTOM PANEL: INTERACTIVE TERMINAL -->
      <section 
        class="terminal-panel {mobileView === 'terminal' ? 'mobile-show' : 'mobile-hide'}" 
        on:click={focusTerminal}
      >
        <div class="terminal-header">
          <div class="terminal-title">
            <i class="fas fa-terminal"></i> sainath@desktop: {currentDir} (zsh)
          </div>
          <div class="terminal-actions">
            <span class="action-btn" title="Clear screen" on:click|stopPropagation={() => executeCommand('clear')} on:keydown={() => {}} role="button" tabindex="0">
              <i class="fas fa-eraser"></i>
            </span>
            <span class="action-btn" title="Scroll to bottom" on:click|stopPropagation={scrollToBottom} on:keydown={() => {}} role="button" tabindex="0">
              <i class="fas fa-angle-double-down"></i>
            </span>
          </div>
        </div>

        <div class="terminal-history" bind:this={terminalOutputRef}>
          <!-- History Log output -->
          {#each terminalHistory as log}
            <div class="history-row {log.type}">
              {#if log.type === "input"}
                <!-- prompt style -->
                <pre class="log-text">{log.text}</pre>
              {:else}
                <pre class="log-text">{log.text}</pre>
              {/if}
            </div>
          {/each}

          <!-- Live Command Prompt -->
          <form class="command-prompt-form" on:submit|preventDefault={() => executeCommand(currentCommand)}>
            <span class="prompt-symbol">guest@sainath-os:{currentDir}$</span>
            <input 
              bind:this={terminalInputRef}
              bind:value={currentCommand}
              type="text" 
              class="command-input" 
              autocomplete="off" 
              autocorrect="off" 
              autocapitalize="off" 
              spellcheck="false"
              on:keydown={handleKeyDown}
            />
            <span class="cursor-block"></span>
          </form>
        </div>
      </section>

    </div>
  </div>

  <!-- MOBILE FOOTER NAVIGATION TABS -->
  <nav class="mobile-tabs-bar">
    <button 
      class="mobile-tab-btn {mobileView === 'explorer' ? 'active' : ''}" 
      on:click={() => mobileView = 'explorer'}
    >
      <i class="fas fa-folder-tree"></i>
      <span>Explorer</span>
    </button>
    <button 
      class="mobile-tab-btn {mobileView === 'editor' ? 'active' : ''}" 
      on:click={() => mobileView = 'editor'}
    >
      <i class="fas fa-code"></i>
      <span>Editor</span>
    </button>
    <button 
      class="mobile-tab-btn {mobileView === 'terminal' ? 'active' : ''}" 
      on:click={() => mobileView = 'terminal'}
    >
      <i class="fas fa-terminal"></i>
      <span>Terminal</span>
    </button>
  </nav>

  <!-- MOBILE QUICK SHORTCUT KEYS ROW (Visible above terminal input only on mobile terminal view) -->
  {#if mobileView === 'terminal'}
    <div class="mobile-shortcuts-row">
      <button class="shortcut-btn" on:click={() => executeCommand('help')}>help</button>
      <button class="shortcut-btn" on:click={() => executeCommand('ls')}>ls</button>
      <button class="shortcut-btn" on:click={() => executeCommand('neofetch')}>neofetch</button>
      <button class="shortcut-btn" on:click={() => executeCommand('skills')}>skills</button>
      <button class="shortcut-btn" on:click={() => executeCommand('projects')}>projects</button>
      <button class="shortcut-btn" on:click={() => executeCommand('clear')}>clear</button>
    </div>
  {/if}

  <!-- SCREEN OVERLAY SCANLINES / CRT SHADOWS -->
  {#if crtEffect}
    <div class="crt-vignette"></div>
  {/if}

  <!-- SCREEN SAVER OVERLAY CANVAS -->
  {#if isMatrixActive}
    <div 
      class="matrix-screensaver" 
      role="button" 
      tabindex="0"
      on:click={stopMatrixRain} 
      on:keydown={handleKeyDown}
    >
      <canvas bind:this={matrixCanvas}></canvas>
      <div class="exit-overlay-label">MATRIX SCREENSAVER ACTIVE // CLICK TO EXIT</div>
    </div>
  {/if}
</main>

<style>
  /* VIRTUAL STYLING SYSTEM BASED ON ACTIVE THEME CUSTOM PROPERTIES */

  /* PROFILE CARD AND ABOUT ME GRID */
  .sidebar-profile-card {
    display: flex;
    align-items: center;
    gap: 12px;
    padding: 12px 14px;
    border-bottom: 1px solid var(--border);
    background-color: rgba(0, 0, 0, 0.12);
  }

  .profile-avatar {
    width: 46px;
    height: 46px;
    border-radius: 50%;
    border: 2px solid var(--border);
    box-shadow: var(--border-glow);
    object-fit: cover;
    background-color: #fff;
    transition: border-color 0.2s ease;
  }

  .sidebar-profile-card:hover .profile-avatar {
    border-color: var(--accent);
  }

  .profile-info {
    display: flex;
    flex-direction: column;
    gap: 2px;
    overflow: hidden;
  }

  .profile-name {
    margin: 0;
    font-size: 13px;
    font-weight: bold;
    color: var(--accent);
    text-overflow: ellipsis;
    white-space: nowrap;
    overflow: hidden;
    letter-spacing: 0.5px;
  }

  .profile-title {
    font-size: 11px;
    color: var(--text-dim);
    text-overflow: ellipsis;
    white-space: nowrap;
    overflow: hidden;
  }

  .about-rendered-view {
    max-width: 850px;
    margin: 0 auto;
    padding: 10px;
  }

  .about-grid {
    display: flex;
    gap: 28px;
    align-items: flex-start;
  }

  .about-avatar-column {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 12px;
    flex-shrink: 0;
  }

  .avatar-frame {
    position: relative;
    width: 140px;
    height: 140px;
    border: 2px solid var(--border);
    border-radius: 8px;
    overflow: hidden;
    box-shadow: var(--border-glow);
    background-color: #fff;
  }

  .about-avatar-img {
    width: 100%;
    height: 100%;
    object-fit: cover;
    opacity: 0.85;
    transition: opacity 0.3s ease;
  }

  .avatar-frame:hover .about-avatar-img {
    opacity: 1;
  }

  .frame-overlay {
    position: absolute;
    top: 0; left: 0; right: 0; bottom: 0;
    background: linear-gradient(rgba(18, 16, 16, 0) 50%, rgba(0, 0, 0, 0.1) 50%);
    background-size: 100% 4px;
    pointer-events: none;
  }

  .system-badge {
    font-size: 10px;
    font-weight: bold;
    color: var(--accent);
    border: 1px solid var(--accent);
    padding: 2px 8px;
    border-radius: 3px;
    box-shadow: var(--border-glow);
    background-color: var(--tab-active);
    letter-spacing: 1px;
  }

  .about-text-column {
    flex-grow: 1;
  }

  :global(body) {
    margin: 0;
    padding: 0;
    overflow: hidden;
    background-color: var(--bg, #040804) !important;
    color: var(--text, #4af626) !important;
    font-family: 'JetBrains Mono', 'Fira Code', monospace;
    font-size: 14px;
    height: 100vh;
    width: 100vw;
    transition: background-color 0.25s ease, color 0.25s ease;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-rendering: optimizeLegibility;
  }

  .terminal-os {
    display: flex;
    flex-direction: column;
    height: 100vh;
    width: 100vw;
    background-color: var(--bg);
    color: var(--text);
    overflow: hidden;
    position: relative;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-rendering: optimizeLegibility;
  }

  /* CRT SCANLINE & VIGNETTE FILTERS */
  .crt-effect::after {
    content: " ";
    display: block;
    position: fixed;
    top: 0;
    left: 0;
    bottom: 0;
    right: 0;
    background: linear-gradient(rgba(18, 16, 16, 0) 50%, rgba(0, 0, 0, 0.07) 50%), 
                linear-gradient(90deg, rgba(255, 0, 0, 0.015), rgba(0, 255, 0, 0.005), rgba(0, 0, 255, 0.015));
    z-index: 9999;
    background-size: 100% 2px, 3px 100%;
    pointer-events: none;
  }

  .crt-vignette {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background: radial-gradient(circle, transparent 75%, rgba(0, 0, 0, 0.25) 100%);
    pointer-events: none;
    z-index: 9998;
  }

  /* SYSTEM MENU TOP BAR */
  .top-bar {
    display: flex;
    align-items: center;
    justify-content: space-between;
    height: 38px;
    background-color: var(--sidebar-bg);
    border-bottom: 1px solid var(--border);
    padding: 0 16px;
    font-size: 13px;
    user-select: none;
    box-shadow: 0 1px 8px rgba(0,0,0,0.2);
    z-index: 50;
  }

  .system-brand {
    display: flex;
    align-items: center;
    gap: 8px;
    font-weight: bold;
    color: var(--accent);
  }

  .pulse-dot {
    width: 8px;
    height: 8px;
    border-radius: 50%;
    background-color: var(--accent);
    box-shadow: 0 0 8px var(--accent);
    animation: blinker 1.8s infinite;
  }

  @keyframes blinker {
    50% { opacity: 0.25; }
  }

  .theme-quick-selector {
    display: flex;
    align-items: center;
    gap: 6px;
  }

  .meta-label {
    color: var(--text-dim);
    font-weight: 500;
  }

  .theme-btn {
    background: transparent;
    border: 1px solid var(--border);
    color: var(--text-dim);
    padding: 2px 8px;
    font-size: 11px;
    font-family: inherit;
    cursor: pointer;
    border-radius: 3px;
    transition: all 0.2s ease;
  }

  .theme-btn:hover, .theme-btn.active {
    color: var(--accent);
    border-color: var(--accent);
    background-color: var(--tab-active);
    box-shadow: var(--border-glow);
  }

  .top-bar-meta {
    display: flex;
    align-items: center;
    gap: 16px;
    color: var(--text-dim);
  }

  .crt-toggle-btn {
    background: transparent;
    border: 1px solid var(--border);
    color: var(--text-dim);
    padding: 2px 8px;
    font-size: 11px;
    font-family: inherit;
    cursor: pointer;
    border-radius: 3px;
  }

  .crt-toggle-btn.active {
    border-color: var(--accent);
    color: var(--accent);
    box-shadow: var(--border-glow);
  }

  .meta-item {
    font-size: 12px;
  }

  .meta-item i {
    margin-right: 4px;
  }

  /* WORKSPACE LAYOUT (DESKTOP) */
  .desktop-workspace {
    display: flex;
    flex-grow: 1;
    height: calc(100vh - 38px);
    width: 100vw;
    overflow: hidden;
  }

  /* SIDEBAR FILE EXPLORER */
  .sidebar-panel {
    width: 250px;
    background-color: var(--sidebar-bg);
    border-right: 1px solid var(--border);
    display: flex;
    flex-direction: column;
    flex-shrink: 0;
  }

  .panel-header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 10px 14px;
    border-bottom: 1px solid var(--border);
    background-color: rgba(0,0,0,0.15);
    font-size: 12px;
    font-weight: bold;
    letter-spacing: 0.5px;
    color: var(--accent);
  }

  .window-actions {
    display: flex;
    gap: 6px;
  }

  .dot {
    width: 8px;
    height: 8px;
    border-radius: 50%;
    background-color: var(--border);
    display: inline-block;
  }
  .dot.close { background-color: #ff5f56; }
  .dot.minimize { background-color: #ffbd2e; }
  .dot.maximize { background-color: #27c93f; }

  .explorer-content {
    flex-grow: 1;
    overflow-y: auto;
    padding: 12px;
  }

  .vfs-root {
    display: flex;
    flex-direction: column;
  }

  .root-name {
    font-size: 13px;
    font-weight: 500;
    color: var(--accent);
    margin-bottom: 8px;
    display: flex;
    align-items: center;
    gap: 6px;
  }

  .tree-items {
    display: flex;
    flex-direction: column;
    gap: 4px;
  }

  .tree-item {
    background: transparent;
    border: none;
    color: var(--text-dim);
    text-align: left;
    font-family: inherit;
    font-size: 13px;
    padding: 4px 8px;
    cursor: pointer;
    display: flex;
    align-items: center;
    gap: 8px;
    width: 100%;
    border-radius: 3px;
    transition: all 0.15s ease;
  }

  .tree-item:hover {
    color: var(--text);
    background-color: var(--tab-active);
  }

  .tree-item.active {
    color: var(--accent);
    background-color: var(--tab-active);
    border-left: 2px solid var(--accent);
    padding-left: 6px;
  }

  .item-label {
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
  }

  .tree-folder {
    display: flex;
    flex-direction: column;
  }

  .folder-children {
    padding-left: 16px;
    border-left: 1px dotted var(--border);
    margin-left: 14px;
    margin-top: 4px;
    margin-bottom: 4px;
    display: flex;
    flex-direction: column;
    gap: 4px;
  }

  /* WORKSPACE CONTENT PANELS (EDITOR & TERMINAL) */
  .workspace-contents {
    display: flex;
    flex-direction: column;
    flex-grow: 1;
    overflow: hidden;
  }

  /* TABBED EDITOR WINDOW */
  .editor-panel {
    flex-grow: 1;
    display: flex;
    flex-direction: column;
    background-color: var(--editor-bg);
    overflow: hidden;
  }

  .editor-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    background-color: var(--sidebar-bg);
    border-bottom: 1px solid var(--border);
    height: 38px;
  }

  .editor-tabs {
    display: flex;
    height: 100%;
    overflow-x: auto;
  }

  .tab-item {
    background: transparent;
    border: none;
    border-right: 1px solid var(--border);
    color: var(--text-dim);
    font-family: inherit;
    font-size: 13px;
    padding: 0 14px;
    display: flex;
    align-items: center;
    gap: 8px;
    cursor: pointer;
    height: 100%;
    position: relative;
    transition: all 0.15s ease;
  }

  .tab-item:hover {
    color: var(--text);
    background-color: rgba(255,255,255,0.02);
  }

  .tab-item.active {
    color: var(--accent);
    background-color: var(--editor-bg);
    border-top: 2px solid var(--accent);
  }

  .tab-close-btn {
    opacity: 0.4;
    font-size: 10px;
    display: inline-flex;
    align-items: center;
    justify-content: center;
    width: 14px;
    height: 14px;
    border-radius: 50%;
    transition: all 0.15s ease;
  }

  .tab-close-btn:hover {
    opacity: 1;
    background-color: rgba(255,255,255,0.1);
  }

  .no-tabs-label {
    color: var(--text-dim);
    font-style: italic;
    align-self: center;
    padding-left: 16px;
    font-size: 12px;
  }

  .editor-body {
    flex-grow: 1;
    display: flex;
    overflow-y: auto;
    position: relative;
  }

  .line-numbers-col {
    display: flex;
    flex-direction: column;
    padding: 16px 8px;
    background-color: rgba(0,0,0,0.1);
    border-right: 1px solid var(--border);
    user-select: none;
    text-align: right;
    min-width: 42px;
    flex-shrink: 0;
  }

  .line-num {
    color: var(--text-dim);
    font-size: 12px;
    line-height: 1.5;
    opacity: 0.5;
  }

  .code-view-col {
    flex-grow: 1;
    padding: 16px;
    overflow-x: auto;
  }

  .code-block {
    margin: 0;
    font-family: inherit;
    font-size: 14px;
    line-height: 1.5;
    color: var(--text);
    white-space: pre;
  }

  .code-block.plain-text {
    white-space: pre-wrap;
    word-break: break-word;
  }

  /* MARKDOWN RENDERED SCREEN */
  .markdown-rendered-view {
    font-family: inherit;
    max-width: 800px;
    margin: 0 auto;
    padding: 8px 16px;
    color: var(--text);
  }

  .project-header-panel {
    border-bottom: 2px dashed var(--border);
    padding-bottom: 12px;
    margin-bottom: 18px;
  }

  .project-label {
    font-size: 11px;
    color: var(--text-dim);
    letter-spacing: 2px;
    display: block;
    margin-bottom: 4px;
  }

  .project-title {
    color: var(--accent);
    font-size: 24px;
    margin: 0 0 6px 0;
    font-weight: bold;
    text-shadow: var(--border-glow);
  }

  .project-tags {
    margin: 0;
    font-size: 13px;
  }

  .tag-title {
    color: var(--text-dim);
  }

  .tag-content {
    color: var(--accent);
  }

  .project-img-frame {
    position: relative;
    border: 1px solid var(--border);
    border-radius: 4px;
    overflow: hidden;
    margin-bottom: 18px;
    box-shadow: var(--border-glow);
    background-color: #000;
    max-height: 380px;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .project-img {
    max-width: 100%;
    max-height: 380px;
    object-fit: contain;
    opacity: 0.85;
    transition: opacity 0.3s ease;
  }

  .project-img-frame:hover .project-img {
    opacity: 1;
  }

  .frame-scanlines {
    position: absolute;
    top:0; left:0; right:0; bottom:0;
    background: linear-gradient(rgba(18, 16, 16, 0) 50%, rgba(0, 0, 0, 0.15) 50%);
    background-size: 100% 4px;
    pointer-events: none;
  }

  .description-text {
    font-size: 14px;
    line-height: 1.6;
    white-space: pre-wrap;
  }

  .empty-editor-splash {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    width: 100%;
    height: 100%;
    padding: 32px;
    text-align: center;
    color: var(--text-dim);
  }

  .splash-icon {
    font-size: 48px;
    color: var(--border);
    margin-bottom: 16px;
    text-shadow: var(--border-glow);
  }

  .empty-editor-splash h3 {
    margin: 0 0 8px 0;
    color: var(--text);
  }

  .empty-editor-splash p {
    max-width: 400px;
    margin: 0;
    font-size: 13px;
  }

  /* INTERACTIVE SHELL CONTACT FORM */
  .contact-script-form {
    display: flex;
    flex-direction: column;
    max-width: 750px;
    margin: 0 auto;
    width: 100%;
  }

  .shell-fields {
    margin-top: 16px;
    display: flex;
    flex-direction: column;
    gap: 12px;
    border: 1px dashed var(--border);
    padding: 20px;
    border-radius: 4px;
    background-color: rgba(0,0,0,0.15);
  }

  .field-row {
    display: flex;
    flex-direction: column;
    gap: 6px;
  }

  .field-prompt {
    font-size: 13px;
    color: var(--text-dim);
    font-weight: 500;
  }

  .field-input, .field-textarea {
    background-color: var(--bg-terminal);
    border: 1px solid var(--border);
    border-radius: 3px;
    color: var(--accent);
    font-family: inherit;
    font-size: 14px;
    padding: 8px 12px;
    outline: none;
    transition: all 0.2s ease;
  }

  .field-input:focus, .field-textarea:focus {
    border-color: var(--accent);
    box-shadow: var(--border-glow);
  }

  .field-textarea {
    resize: vertical;
    min-height: 100px;
  }

  .action-row {
    margin-top: 8px;
    text-align: right;
  }

  .submit-sh-btn, .reset-sh-btn {
    background-color: var(--tab-active);
    color: var(--accent);
    border: 1px solid var(--accent);
    border-radius: 4px;
    padding: 8px 16px;
    font-family: inherit;
    font-size: 13px;
    cursor: pointer;
    box-shadow: var(--border-glow);
    font-weight: bold;
    transition: all 0.2s ease;
  }

  .submit-sh-btn:hover, .reset-sh-btn:hover {
    background-color: var(--accent);
    color: var(--bg);
  }

  .transmission-logs {
    border: 1px solid var(--border);
    background-color: rgba(0,0,0,0.3);
    padding: 16px;
    border-radius: 4px;
    margin-top: 16px;
    display: flex;
    flex-direction: column;
    gap: 6px;
    min-height: 200px;
  }

  .log-line {
    font-size: 13px;
    line-height: 1.4;
  }

  .log-line.success {
    color: var(--accent);
  }

  .log-line.info {
    color: var(--text-dim);
  }

  .log-status {
    color: var(--accent);
    margin-right: 8px;
    font-weight: bold;
  }

  .blink-cursor-inline {
    width: 8px;
    height: 15px;
    background-color: var(--text);
    display: inline-block;
    animation: cursor-blink 1s step-end infinite;
  }

  .transmission-success {
    margin-top: 16px;
  }

  .terminal-card-success {
    border: 1px solid var(--accent);
    background-color: rgba(0, 255, 0, 0.03);
    padding: 24px;
    border-radius: 4px;
    text-align: center;
    position: relative;
    box-shadow: var(--border-glow);
  }

  .card-title {
    color: var(--accent);
    margin-top: 0;
    margin-bottom: 12px;
    font-weight: bold;
  }

  .card-msg {
    margin-bottom: 20px;
    font-size: 14px;
    line-height: 1.5;
  }

  .card-meta-info {
    display: flex;
    justify-content: center;
    gap: 24px;
    font-size: 11px;
    color: var(--text-dim);
    margin-bottom: 24px;
    border-top: 1px dashed var(--border);
    padding-top: 12px;
  }

  /* BOTTOM TERMINAL PANEL */
  .terminal-panel {
    height: 260px;
    background-color: var(--bg-terminal);
    border-top: 1px solid var(--border);
    display: flex;
    flex-direction: column;
    overflow: hidden;
    flex-shrink: 0;
  }

  .terminal-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    background-color: var(--sidebar-bg);
    border-bottom: 1px solid var(--border);
    padding: 6px 14px;
    height: 32px;
    user-select: none;
  }

  .terminal-title {
    font-size: 12px;
    font-weight: 500;
    color: var(--text-dim);
  }

  .terminal-actions {
    display: flex;
    gap: 12px;
  }

  .action-btn {
    color: var(--text-dim);
    cursor: pointer;
    font-size: 12px;
    transition: color 0.15s ease;
  }

  .action-btn:hover {
    color: var(--accent);
  }

  .terminal-history {
    flex-grow: 1;
    overflow-y: auto;
    padding: 12px;
    display: flex;
    flex-direction: column;
    gap: 4px;
  }

  .history-row {
    font-size: 13px;
    line-height: 1.4;
  }

  .history-row pre {
    margin: 0;
    font-family: inherit;
    white-space: pre-wrap;
    word-break: break-all;
  }

  .history-row.input {
    color: var(--accent);
  }

  .history-row.error {
    color: #ff5f56;
  }

  .history-row.success {
    color: var(--accent);
  }

  .history-row.system {
    color: var(--text-dim);
    opacity: 0.6;
  }

  .history-row.info {
    color: var(--accent);
    opacity: 0.85;
  }

  .command-prompt-form {
    display: flex;
    align-items: center;
    position: relative;
    width: 100%;
    margin-top: 4px;
  }

  .prompt-symbol {
    color: var(--accent);
    margin-right: 8px;
    font-size: 13px;
    white-space: nowrap;
    user-select: none;
  }

  .command-input {
    background: transparent;
    border: none;
    outline: none;
    color: var(--text);
    font-family: inherit;
    font-size: 13px;
    flex-grow: 1;
    caret-color: transparent; /* custom cursor rendered */
  }

  .cursor-block {
    width: 8px;
    height: 15px;
    background-color: var(--text);
    position: absolute;
    animation: cursor-blink 1s step-end infinite;
    pointer-events: none;
    display: inline-block;
  }

  /* Setup dynamic block cursor position */
  .command-prompt-form:focus-within .cursor-block {
    display: inline-block;
  }

  @keyframes cursor-blink {
    from, to { background-color: transparent }
    50% { background-color: var(--text) }
  }

  /* SYNTAX HIGHLIGHT COLOR CLASS RULES */
  :global(.json-key) { color: #f92672; }
  :global(.json-string) { color: var(--accent); }
  :global(.json-number) { color: #ae81ff; }
  :global(.json-boolean) { color: #66d9ef; }
  :global(.json-null) { color: #75715e; }

  :global(.sh-comment) { color: var(--text-dim); opacity: 0.6; }
  :global(.sh-cmd) { color: #66d9ef; font-weight: bold; }
  :global(.sh-string) { color: var(--accent); }

  /* MOBILE RESPONSIVE TABS & FOOTER NAVIGATION */
  .mobile-tabs-bar {
    display: none;
    height: 48px;
    background-color: var(--sidebar-bg);
    border-top: 1px solid var(--border);
    justify-content: space-around;
    align-items: center;
    z-index: 50;
  }

  .mobile-tab-btn {
    background: transparent;
    border: none;
    color: var(--text-dim);
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 4px;
    font-family: inherit;
    font-size: 10px;
    cursor: pointer;
    padding: 4px 0;
    flex-grow: 1;
    transition: color 0.15s ease;
  }

  .mobile-tab-btn.active {
    color: var(--accent);
    text-shadow: var(--border-glow);
  }

  .mobile-tab-btn i {
    font-size: 16px;
  }

  .mobile-shortcuts-row {
    display: none;
    gap: 8px;
    padding: 8px 12px;
    background-color: rgba(0,0,0,0.2);
    overflow-x: auto;
    border-top: 1px solid var(--border);
    white-space: nowrap;
  }

  .shortcut-btn {
    background-color: var(--sidebar-bg);
    color: var(--text);
    border: 1px solid var(--border);
    border-radius: 4px;
    padding: 4px 10px;
    font-family: inherit;
    font-size: 11px;
    cursor: pointer;
  }

  .shortcut-btn:active {
    background-color: var(--accent);
    color: var(--bg);
  }

  /* CANVAS MATRIX SCREEN SAVER */
  .matrix-screensaver {
    position: fixed;
    top: 0; left: 0; right: 0; bottom: 0;
    z-index: 9995;
    background-color: #000;
    cursor: pointer;
  }

  .exit-overlay-label {
    position: absolute;
    bottom: 24px;
    left: 50%;
    transform: translateX(-50%);
    background-color: rgba(0,0,0,0.85);
    border: 1px solid var(--border);
    color: var(--text);
    padding: 8px 16px;
    font-size: 12px;
    border-radius: 4px;
    letter-spacing: 1px;
    animation: pulse 2s infinite;
    pointer-events: none;
  }

  @keyframes pulse {
    0%, 100% { opacity: 0.6; }
    50% { opacity: 1; }
  }

  /* MEDIA QUERIES FOR MOBILE COMPATIBILITY */
  @media (max-width: 767px) {
    .top-bar-meta .meta-item:not(.clock) {
      display: none;
    }

    .theme-quick-selector {
      display: none;
    }

    .desktop-workspace {
      flex-direction: column;
      height: calc(100vh - 86px); /* account for top OS bar and mobile bottom tab nav */
    }

    .sidebar-panel.mobile-hide,
    .editor-panel.mobile-hide,
    .terminal-panel.mobile-hide {
      display: none !important;
    }

    .sidebar-panel.mobile-show,
    .editor-panel.mobile-show,
    .terminal-panel.mobile-show {
      display: flex !important;
      width: 100% !important;
      height: 100% !important;
      border: none !important;
    }

    .mobile-tabs-bar {
      display: flex;
    }

    .mobile-shortcuts-row {
      display: flex;
    }

    .line-numbers-col {
      min-width: 32px;
      padding: 12px 4px;
    }

    .code-view-col {
      padding: 12px;
    }

    .code-block {
      font-size: 12px;
    }

    .project-title {
      font-size: 20px;
    }

    .project-img-frame {
      max-height: 200px;
    }

    .project-img {
      max-height: 200px;
    }

    .about-grid {
      flex-direction: column;
      align-items: center;
      gap: 16px;
    }
  }
</style>
