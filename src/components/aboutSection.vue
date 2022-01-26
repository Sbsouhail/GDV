<template>
  <div class="d-flex cutomContainer">
    <a
      id="about"
      style="display: block; position: relative; top: -70px; visibility: hidden"
    ></a>
    <div class="col-6 left">
      <div class="underline"><p>About</p></div>
      <h5>
        Open source version control software that allows multiple people to make
        separate changes to web pages at the same time. It enables real-time
        collaboration and encourages teams to work together to build and edit
        content for their site.
      </h5>
    </div>
    <div class="col-6 picture"></div>
  </div>
</template>
<style lang="scss" scoped>
.cutomContainer {
  .left {
    padding: 3% 10%;
    .underline {
      border-bottom: 5px solid #f44336;
      margin-bottom: 40px;
      width: fit-content;
      p {
        font-weight: bold;
        font-size: 60px;
      }
    }
  }
  .picture {
    background-image: linear-gradient(
        rgba(4, 9, 30, 0.555),
        rgba(4, 9, 30, 0.555)
      ),
      url(../assets/pic.jpg);
    background-attachment: fixed;
    background-position: bottom;
    background-size: cover;
  }
}
@media (max-width: 700px) {
  .cutomContainer {
    .picture {
      display: none;
    }
    .left {
      width: 100%;
      p {
        font-size: 40px !important;
      }
    }
  }
}
</style>