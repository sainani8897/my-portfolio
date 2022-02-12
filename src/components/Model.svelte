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
    portfolioData = data
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


</script>
<Modal isOpen={open} {toggle} {size}>
    <ModalBody>
      <div class="container">
        <div class="row justify-content-center">
          <div class="col-lg-8">
            <!-- Portfolio Modal - Title-->
            <h2 class="portfolio-modal-title text-secondary text-uppercase mb-0">
              HIVE BI
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
              src={portfolioData.img}
              alt="..."
            />
            <!-- Portfolio Modal - Text-->
            <p class="mb-4">
              Hive BI is an Bussiness Intelligence (ERP & CMS) Application.
            </p>
          </div>
        </div>
      </div>
    </ModalBody>
    <ModalFooter>
      <Button color="secondary" on:click={toggle}>Close</Button>
    </ModalFooter>
  </Modal>