<!-- Portfolio Modal 2-->
<script>
  import {
    Button,
    ButtonGroup,
    Modal,
    ModalBody,
    ModalFooter,
    ModalHeader,
  } from "sveltestrap";
  let open = false;
  let size = "";
  let portfolioData = {};
  const toggle = () => {
    size = undefined;
    open = !open;
  };
  const toggleLg = (data) => {
    size = "lg";
    open = !open;
    portfolioData = data;
    //console.log(portfolioData);
  };
  const toggleSm = () => {
    size = "sm";
    open = !open;
  };
  const toggleXl = () => {
    size = "xl";
    open = !open;
  };
  let isOpen = false;
  //   const toggle = () => (isOpen = !isOpen);
</script>

<section class="page-section portfolio" id="portfolio">
  <div class="container">
    <!-- Portfolio Section Heading-->
    <h2
      class="page-section-heading text-center text-uppercase text-secondary mb-0"
    >
      Portfolio
    </h2>
    <!-- Icon Divider-->
    <div class="divider-custom">
      <div class="divider-custom-line" />
      <div class="divider-custom-icon"><i class="fas fa-star" /></div>
      <div class="divider-custom-line" />
    </div>
    <!-- Portfolio Grid Items-->
    <div class="row justify-content-center">
      <!-- Portfolio Item 1-->
      <div class="col-md-6 col-lg-4 mb-5"  on:click={() => {
        toggleLg({ img: "assets/img/portfolio/bi-2.png",content:"Hive BI is an Bussiness Intelligence (ERP & CMS) Application.",title:"HIVE BI" });
      }}>
        <div
          class="portfolio-item mx-auto"
          data-bs-toggle="modal"
          data-bs-target="#portfolioModal1"
        >
          <div
            class="portfolio-item-caption d-flex align-items-center justify-content-center h-100 w-100"
          >
            <div class="portfolio-item-caption-content text-center text-white">
              <i class="fas fa-plus fa-3x" />
            </div>
          </div>
          <img
            class="img-fluid"
            src="assets/img/portfolio/bi-2.png"
            alt="..."
          />
        </div>
      </div>
      <!-- Portfolio Item 2-->
      <div
        class="col-md-6 col-lg-4 mb-5"
        on:click={() => {
          toggleLg({ img: "assets/img/portfolio/covid-help-source.png",content:"",title:"Covid Resource" });
        }}
      >
        <div
          class="portfolio-item mx-auto"
          data-bs-toggle="modal"
          data-bs-target="#portfolioModal2"
        >
          <div
            class="portfolio-item-caption d-flex align-items-center justify-content-center h-100 w-100"
          >
            <div class="portfolio-item-caption-content text-center text-white">
              <i class="fas fa-plus fa-3x" />
            </div>
          </div>
          <img
            class="img-fluid"
            src="assets/img/portfolio/covid-help-source1.png"
            alt="..."
          />
        </div>
      </div>
      <!-- Portfolio Item 3-->
      <div class="col-md-6 col-lg-4 mb-5">
        <div
          class="portfolio-item mx-auto"
          data-bs-toggle="modal"
          data-bs-target="#portfolioModal3"
        >
          <div
            class="portfolio-item-caption d-flex align-items-center justify-content-center h-100 w-100"
            on:click={() => {
              toggleLg({ img: "assets/img/portfolio/24-7-kirana-dashboardmobile-home.png",content:"24x7 Kirana is online grocery store. With over 1,000 products and over a 100 brands in our catalogue you will find everything. Buy premium quality of grocery like atta, rice, dal, spices, and masala online from home with free shipping option and get it delivered anywhere in the Hyderabad. Choose from a wide range of options in every category, you find the best quality available at the lowest prices. Select a time slot for delivery and your order will be delivered right to your doorstep, anywhere in Hyderabad.",title:"24x7 KIRANA" });
            }}
          >
            <div class="portfolio-item-caption-content text-center text-white">
              <i class="fas fa-plus fa-3x" />
            </div>
          </div>
          <img
            class="img-fluid"
            src="assets/img/portfolio/24-7-kirana-dashboardmobile-home.png"
            alt="..."
          />
        </div>
      </div>
      <!-- Portfolio Item 4-->
      <div class="col-md-6 col-lg-4 mb-5 mb-lg-0">
        <div
          class="portfolio-item mx-auto"
          data-bs-toggle="modal"
          data-bs-target="#portfolioModal4"
        >
          <div
            class="portfolio-item-caption d-flex align-items-center justify-content-center h-100 w-100"
            on:click={() => {
              toggleLg({ img: "assets/img/portfolio/property-nidhihome.png",content:"Property Nidhi.com is providing all the information clearly about the real estate business. In India, real estate is an industry that is developing day by day with lightning speed. The real estate sector brings a lot of profits to the people who are investing in it. People use many e-portals to get information about the property services but not satisfied with the incomplete information they are providing. Propertynidhi.com is a website where you can find all the information about the property, property services, and more information related to the real estate business. You can find a good property with a reasonable investment in a short period. We respect and care about our clients. We provide the services in such a way that our users get benefited through it. Taking all the precautions while you are investing in the property will be helpful for your future generations too. Use all the services of propertynidhi.com and make your dreams real.",title:"Property Nidhi" });
            }}
          >
            <div class="portfolio-item-caption-content text-center text-white">
              <i class="fas fa-plus fa-3x" />
            </div>
          </div>
          <img
            class="img-fluid"
            src="assets/img/portfolio/property-nidhihome.png"
            alt="property-home"
          />
        </div>
      </div>
      <!-- Portfolio Item 5-->
      <div class="col-md-6 col-lg-4 mb-5 mb-md-0">
        <div
          class="portfolio-item mx-auto"
          data-bs-toggle="modal"
          data-bs-target="#portfolioModal5"
        >
          <div
            class="portfolio-item-caption d-flex align-items-center justify-content-center h-100 w-100"
          >
            <div class="portfolio-item-caption-content text-center text-white">
              <i class="fas fa-plus fa-3x" />
            </div>
          </div>
          <img
            class="img-fluid"
            src="assets/img/portfolio/safe.png"
            alt="..."
          />
        </div>
      </div>
      <!-- Portfolio Item 6-->
      <div class="col-md-6 col-lg-4">
        <div
          class="portfolio-item mx-auto"
          data-bs-toggle="modal"
          data-bs-target="#portfolioModal6"
        >
          <div
            class="portfolio-item-caption d-flex align-items-center justify-content-center h-100 w-100"
          >
            <div class="portfolio-item-caption-content text-center text-white">
              <i class="fas fa-plus fa-3x" />
            </div>
          </div>
          <img
            class="img-fluid"
            src="assets/img/portfolio/submarine.png"
            alt="..."
          />
        </div>
      </div>
    </div>
  </div>
</section>
<!-- About Section-->
<section class="page-section bg-primary text-white mb-0" id="about">
  <div class="container">
    <!-- About Section Heading-->
    <h2 class="page-section-heading text-center text-uppercase text-white">
      About
    </h2>
    <!-- Icon Divider-->
    <div class="divider-custom divider-light">
      <div class="divider-custom-line" />
      <div class="divider-custom-icon"><i class="fas fa-star" /></div>
      <div class="divider-custom-line" />
    </div>
    <!-- About Section Content-->
    <div class="row">
      <div class="col-lg-4 ms-auto">
        <p class="lead">
          Freelancer is a free bootstrap theme created by Start Bootstrap. The
          download includes the complete source files including HTML, CSS, and
          JavaScript as well as optional SASS stylesheets for easy
          customization.
        </p>
      </div>
      <div class="col-lg-4 me-auto">
        <p class="lead">
          You can create your own custom avatar for the masthead, change the
          icon in the dividers, and add your email address to the contact form
          to make it fully functional!
        </p>
      </div>
    </div>
    <!-- About Section Button-->
    <div class="text-center mt-4">
      <a
        class="btn btn-xl btn-outline-light"
        target="_blank"
        href="https://docs.google.com/document/d/1-HlZx3-BzSQz6nNhDPL77tPLImw_nQjAtWOICQR1UgE/edit?usp=sharing"
      >
        <i class="fas fa-download me-2" />
        Resume
      </a>
    </div>
  </div>
</section>
<!-- Contact Section-->
<section class="page-section" id="contact">
  <div class="container">
    <!-- Contact Section Heading-->
    <h2
      class="page-section-heading text-center text-uppercase text-secondary mb-0"
    >
      Hire Me
    </h2>
    <!-- Icon Divider-->
    <div class="divider-custom">
      <div class="divider-custom-line" />
      <div class="divider-custom-icon"><i class="fas fa-star" /></div>
      <div class="divider-custom-line" />
    </div>
    <!-- Contact Section Form-->
    <div class="row justify-content-center">
      <div class="col-lg-8 col-xl-7">
        <!-- * * * * * * * * * * * * * * *-->
        <!-- * * SB Forms Contact Form * *-->
        <!-- * * * * * * * * * * * * * * *-->
        <!-- This form is pre-integrated with SB Forms.-->
        <!-- To make this form functional, sign up at-->
        <!-- https://startbootstrap.com/solution/contact-forms-->
        <!-- to get an API token!-->
        <form id="contactForm" data-sb-form-api-token="API_TOKEN">
          <!-- Name input-->
          <div class="form-floating mb-3">
            <input
              class="form-control"
              id="name"
              type="text"
              placeholder="Enter your name..."
              data-sb-validations="required"
            />
            <label for="name">Full name</label>
            <div class="invalid-feedback" data-sb-feedback="name:required">
              A name is required.
            </div>
          </div>
          <!-- Email address input-->
          <div class="form-floating mb-3">
            <input
              class="form-control"
              id="email"
              type="email"
              placeholder="name@example.com"
              data-sb-validations="required,email"
            />
            <label for="email">Email address</label>
            <div class="invalid-feedback" data-sb-feedback="email:required">
              An email is required.
            </div>
            <div class="invalid-feedback" data-sb-feedback="email:email">
              Email is not valid.
            </div>
          </div>
          <!-- Phone number input-->
          <div class="form-floating mb-3">
            <input
              class="form-control"
              id="phone"
              type="tel"
              placeholder="(123) 456-7890"
              data-sb-validations="required"
            />
            <label for="phone">Phone number</label>
            <div class="invalid-feedback" data-sb-feedback="phone:required">
              A phone number is required.
            </div>
          </div>
          <!-- Message input-->
          <div class="form-floating mb-3">
            <textarea
              class="form-control"
              id="message"
              type="text"
              placeholder="Enter your message here..."
              style="height: 10rem"
              data-sb-validations="required"
            />
            <label for="message">Message</label>
            <div class="invalid-feedback" data-sb-feedback="message:required">
              A message is required.
            </div>
          </div>
          <!-- Submit success message-->
          <!---->
          <!-- This is what your users will see when the form-->
          <!-- has successfully submitted-->
          <div class="d-none" id="submitSuccessMessage">
            <div class="text-center mb-3">
              <div class="fw-bolder">Form submission successful!</div>
              To activate this form, sign up at
              <br />
              <a href="https://startbootstrap.com/solution/contact-forms"
                >https://startbootstrap.com/solution/contact-forms</a
              >
            </div>
          </div>
          <!-- Submit error message-->
          <!---->
          <!-- This is what your users will see when there is-->
          <!-- an error submitting the form-->
          <div class="d-none" id="submitErrorMessage">
            <div class="text-center text-danger mb-3">
              Error sending message!
            </div>
          </div>
          <!-- Submit Button-->
          <button
            class="btn btn-primary btn-xl disabled"
            id="submitButton"
            type="submit">Send</button
          >
        </form>
      </div>
    </div>
  </div>
</section>

<!-- Portfolio Modal 1-->
<div
  class="portfolio-modal modal fade"
  id="portfolioModal1"
  tabindex="-1"
  aria-labelledby="portfolioModal1"
  aria-hidden="true"
>
  <div class="modal-dialog modal-xl">
    <div class="modal-content">
      <div class="modal-header border-0">
        <button
          class="btn-close"
          type="button"
          data-bs-dismiss="modal"
          aria-label="Close"
        />
      </div>
      <div class="modal-body text-center pb-5">
        <div class="container">
          <div class="row justify-content-center">
            <div class="col-lg-8">
              <!-- Portfolio Modal - Title-->
              <h2
                class="portfolio-modal-title text-secondary text-uppercase mb-0"
              >
                Log Cabin
              </h2>
              <!-- Icon Divider-->
              <div class="divider-custom">
                <div class="divider-custom-line" />
                <div class="divider-custom-icon"><i class="fas fa-star" /></div>
                <div class="divider-custom-line" />
              </div>
              <!-- Portfolio Modal - Image-->
              <img
                class="img-fluid rounded mb-5"
                src="assets/img/portfolio/cabin.png"
                alt="..."
              />
              <!-- Portfolio Modal - Text-->
              <p class="mb-4">
                Lorem ipsum dolor sit amet, consectetur adipisicing elit.
                Mollitia neque assumenda ipsam nihil, molestias magnam,
                recusandae quos quis inventore quisquam velit asperiores, vitae?
                Reprehenderit soluta, eos quod consequuntur itaque. Nam.
              </p>
              <button class="btn btn-primary" href="#!" data-bs-dismiss="modal">
                <i class="fas fa-times fa-fw" />
                Close Window
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</div>

<Modal isOpen={open} {toggle} {size}>
  <ModalBody>
    <div class="container">
      <div class="row justify-content-center">
        <div class="col-lg-8">
          <!-- Portfolio Modal - Title-->
          <h2 class="portfolio-modal-title text-secondary text-uppercase mb-0">
            {portfolioData.title ?? ''}
          </h2>
          <!-- Icon Divider-->
          <div class="divider-custom">
            <div class="divider-custom-line" />
            <div class="divider-custom-icon"><i class="fas fa-star" /></div>
            <div class="divider-custom-line" />
          </div>
          <!-- Portfolio Modal - Image-->
          <img
            class="img-fluid rounded mb-5"
            src="{portfolioData.img ?? ''}"
            alt="..."
          />
          <!-- Portfolio Modal - Text-->

          
          <p class="mb-4">
            {portfolioData.content ?? ''}
          </p>
        </div>
      </div>
    </div>
  </ModalBody>
  <ModalFooter>
    <Button color="secondary" on:click={toggle}>Close</Button>
  </ModalFooter>
</Modal>

<style>
  .portfolio-modal-title {
    text-align: center;
    justify-content: center;
  }
</style>
