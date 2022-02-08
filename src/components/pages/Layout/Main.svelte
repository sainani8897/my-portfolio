<script>
  import Footer from "./../Footer.svelte";
  import Portfolio from "./../Portfolio.svelte";
  import Header from "./../Header.svelte";
</script>

<Header />

<!-- Masthead-->
<header class="masthead bg-primary text-white text-center">
  <div class="container d-flex align-items-center flex-column">
    <!-- Masthead Avatar Image-->
    <img
      class="masthead-avatar mb-5 rounded-circle bg-white "
      src="assets/img/20211217_183735_ccexpress.png"
      alt="Sainath-Dekonda-profile-pic"
    />
    <!-- Masthead Heading-->
    <h1 class="masthead-heading text-uppercase mb-0">Sainath Dekonda</h1>
    <!-- Icon Divider-->
    <div class="divider-custom divider-light">
      <div class="divider-custom-line" />
      <div class="divider-custom-icon"><i class="fas fa-star" /></div>
      <div class="divider-custom-line" />
    </div>
    <!-- Masthead Subheading-->
    <p class="masthead-subheading font-weight-light mb-0">
      Senior - Fullstack Developer - Vue.js - PHP Laravel Node.js
    </p>
  </div>
</header>

<Portfolio />

<Footer />

