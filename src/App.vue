<script setup>
import { onMounted, ref } from "vue";
import { RouterLink, RouterView } from "vue-router";

onMounted(() => {
  init();
});

function addSkill(){

}

function init() {
  const container = document.getElementById("globe");
  const canvas = container.getElementsByTagName("canvas")[0];

  const globeRadius = 100;
  const globeWidth = 4098 / 2;
  const globeHeight = 1968 / 2;

  function convertFlatCoordsToSphereCoords(x, y) {
    let latitude = ((x - globeWidth) / globeWidth) * -180;
    let longitude = ((y - globeHeight) / globeHeight) * -90;
    latitude = (latitude * Math.PI) / 180;
    longitude = (longitude * Math.PI) / 180;
    const radius = Math.cos(longitude) * globeRadius;

    return {
      x: Math.cos(latitude) * radius,
      y: Math.sin(longitude) * globeRadius,
      z: Math.sin(latitude) * radius,
    };
  }

  function makeMagic(points) {
    const { width, height } = container.getBoundingClientRect();

    // 1. Setup scene
    const scene = new THREE.Scene();
    // 2. Setup camera
    const camera = new THREE.PerspectiveCamera(45, width / height);
    // 3. Setup renderer
    const renderer = new THREE.WebGLRenderer({
      canvas,
      antialias: true,
    });
    renderer.setSize(width, height);
    // 4. Add points to canvas
    // - Single geometry to contain all points.
    const mergedGeometry = new THREE.Geometry();
    // - Material that the dots will be made of.
    const pointGeometry = new THREE.SphereGeometry(0.5, 1, 1);
    const pointMaterial = new THREE.MeshBasicMaterial({
      color: "#989db5",
    });

    for (let point of points) {
      const { x, y, z } = convertFlatCoordsToSphereCoords(
        point.x,
        point.y,
        width,
        height
      );

      if (x && y && z) {
        pointGeometry.translate(x, y, z);
        mergedGeometry.merge(pointGeometry);
        pointGeometry.translate(-x, -y, -z);
      }
    }

    const globeShape = new THREE.Mesh(mergedGeometry, pointMaterial);
    scene.add(globeShape);

    container.classList.add("peekaboo");

    // Setup orbital controls
    camera.orbitControls = new THREE.OrbitControls(camera, canvas);
    camera.orbitControls.enableKeys = false;
    camera.orbitControls.enablePan = false;
    camera.orbitControls.enableZoom = false;
    camera.orbitControls.enableDamping = false;
    camera.orbitControls.enableRotate = true;
    camera.orbitControls.autoRotate = true;
    camera.position.z = -265;

    function animate() {
      // orbitControls.autoRotate is enabled so orbitControls.update
      // must be called inside animation loop.
      camera.orbitControls.update();
      requestAnimationFrame(animate);
      renderer.render(scene, camera);
    }
    animate();
  }

  function hasWebGL() {
    const gl =
      canvas.getContext("webgl") || canvas.getContext("experimental-webgl");
    if (gl && gl instanceof WebGLRenderingContext) {
      return true;
    } else {
      return false;
    }
  }

  function init() {
    if (hasWebGL()) {
      window;
      window
        .fetch(
          "https://raw.githubusercontent.com/creativetimofficial/public-assets/master/soft-ui-dashboard-pro/assets/js/points.json"
        )
        .then((response) => response.json())
        .then((data) => {
          makeMagic(data.points);
        });
    }
  }
  init();
}

let show = ref(false);
</script>

<template>
  <main
    class="container position-relative max-height-vh-100 h-100 border-radius-lg"
  >
    <nav
      class="navbar navbar-main navbar-expand-lg position-sticky mt-4 top-1 px-0 mx-4 shadow-none border-radius-xl z-index-sticky"
      id="navbarBlur"
      data-scroll="true"
    >
      <div class="container-fluid py-1 px-3 bg-white w-100">
        <nav class="d-flex align-items-end mb-3 mb-md-0" aria-label="breadcrumb">
          <div>
            <img  class="avatar" src="https://media-exp1.licdn.com/dms/image/C4D03AQHEtMgx8MzJHw/profile-displayphoto-shrink_800_800/0/1621511606507?e=1669248000&v=beta&t=FBNeZ9e4k1GdnY-EjsCydrPCgPmzvtET-KR3R_l8VSk" alt="">
          </div>
          <h6 class="font-weight-bolder ms-3 mb-0">JJenus</h6>
        </nav>
        <form action="">
          <div class="input-group">
            <span class="input-group-text text-body"
              ><i class="fas fa-search" aria-hidden="true"></i
            ></span>
            <input
              type="text"
              class="form-control"
              placeholder="Type here..."
            />
          </div>
        </form>
      </div>
    </nav>

    <div class="container-fluid py-4">
      <div class="row">
        <div class="col-lg-7 position-relative z-index-2">
          <div class="card card-plain mb-4">
            <div class="card-body p-3">
              <div class="row">
                <div class="col-lg-6">
                  <div class="d-flex flex-column h-100">
                    <h2 class="font-weight-bolder mb-0">GIIT Interview</h2>
                    <small>Candidates</small>
                  </div>
                </div>
              </div>
            </div>
          </div>
          <div class="row">
            <div class="col-lg-5 col-sm-6">
              <div class="card mb-4">
                <div class="card-body p-3">
                  <div class="row">
                    <div class="col-8">
                      <div class="numbers">
                        <p
                          class="text-sm mb-0 text-capitalize font-weight-bold"
                        >
                          Total Candidates
                        </p>
                        <h5 class="font-weight-bolder mb-0">
                          53,000
                          <span class="text-success text-sm font-weight-bolder"
                            >top 3%</span
                          >
                        </h5>
                      </div>
                    </div>
                    <div class="col-4 text-end">
                      <div
                        class="icon icon-shape bg-gradient-primary shadow text-center border-radius-md"
                      >
                        <i
                          class="fas fa-user text-lg opacity-10"
                          aria-hidden="true"
                        ></i>
                      </div>
                    </div>
                  </div>
                </div>
              </div>
            </div>
            <div class="col-lg-5 col-sm-6 mt-sm-0 mt-4">
              <div class="card">
                <div class="card-body p-3">
                  <div class="row">
                    <div class="col-8">
                      <div class="numbers">
                        <p
                          class="text-sm mb-0 text-capitalize font-weight-bold"
                        >
                          Available
                        </p>
                        <h5 class="font-weight-bolder mb-0">2,300</h5>
                      </div>
                    </div>
                    <div class="col-4 text-end">
                      <div
                        class="icon icon-shape bg-gradient-primary shadow text-center border-radius-md"
                      >
                        <i
                          class="fas fa-check text-lg opacity-10"
                          aria-hidden="true"
                        ></i>
                      </div>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- Competitors -->

      <div class="row">
        <div class="col-md-4 mt-4">
          <div class="card">
            <img class="card-img-top" src="assets/img/team-1.jpg" />
            <div
              class="position-relative"
              style="
                height: 50px;
                overflow: hidden;
                margin-top: -50px;
                z-index: 2;
                position: relative;
              "
            >
              <div class="position-absolute w-100 top-0" style="z-index: 1">
                <svg
                  class="waves waves-sm"
                  xmlns="http://www.w3.org/2000/svg"
                  xmlns:xlink="http://www.w3.org/1999/xlink"
                  viewBox="0 24 150 40"
                  preserveAspectRatio="none"
                  shape-rendering="auto"
                >
                  <defs>
                    <path
                      id="card-wave"
                      d="M-160 44c30 0 58-18 88-18s 58 18 88 18 58-18 88-18 58 18 88 18 v44h-352z"
                    ></path>
                  </defs>
                  <g class="moving-waves">
                    <use
                      xlink:href="#card-wave"
                      x="48"
                      y="-1"
                      fill="rgba(255,255,255,0.30"
                    ></use>
                    <use
                      xlink:href="#card-wave"
                      x="48"
                      y="3"
                      fill="rgba(255,255,255,0.35)"
                    ></use>
                    <use
                      xlink:href="#card-wave"
                      x="48"
                      y="5"
                      fill="rgba(255,255,255,0.25)"
                    ></use>
                    <use
                      xlink:href="#card-wave"
                      x="48"
                      y="8"
                      fill="rgba(255,255,255,0.20)"
                    ></use>
                    <use
                      xlink:href="#card-wave"
                      x="48"
                      y="13"
                      fill="rgba(255,255,255,0.15)"
                    ></use>
                    <use
                      xlink:href="#card-wave"
                      x="48"
                      y="16"
                      fill="rgba(255,255,255,0.99)"
                    ></use>
                  </g>
                </svg>
              </div>
            </div>
            <div class="card-body">
              <h4>John Dalton</h4>
              <p>Web developer</p>
              <div class="d-flex align-items-center justify-content-center">
                <button type="button" class="btn btn-github btn-icon m-2">
                  <span class="btn-inner--icon"
                    ><i class="fab fs-6 fa-github"></i
                  ></span>
                </button>
                <button type="button" class="btn btn-linkedin btn-icon m-2">
                  <span class="btn-inner--icon"
                    ><i class="fab fs-6 fa-linkedin"></i
                  ></span>
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- huy -->
      <div class="row">
        <div class="col-12">
          <div
            id="globe"
            class="position-absolute end-0 top-10 mt-sm-3 mt-7 me-lg-7"
          >
            <canvas
              width="700"
              height="600"
              class="w-lg-100 h-lg-100 w-75 h-75 me-lg-0 me-n10 mt-lg-5"
            ></canvas>
          </div>
        </div>
      </div>
      <footer class="footer pt-3">
        <div class="container-fluid">
          <div class="row align-items-center justify-content-lg-between">
            <div class="col-lg-6 mb-lg-0 mb-4">
              <div
                class="copyright text-center text-sm text-muted text-lg-start"
              >
                © 2022, credits:
                <a
                  href="https://www.creative-tim.com/"
                  class="font-weight-bold"
                  target="_blank"
                  >Creative Tim</a
                >
                soft ui
              </div>
            </div>
          </div>
        </div>
      </footer>
    </div>
  </main>

  <div :class="show ? 'show' : ''" class="fixed-plugin ">
    <a
      @click="show = true"
      class="fixed-plugin-button text-dark position-fixed px-3 py-2"
    >
      <i class="fa fa-plus py-2"> </i>
    </a>
    <div class="card shadow-lg blur overflow-scroll">
      <div class="card-header pb-0 pt-3 bg-transparent">
        <div class="float-start">
          <h5 class="mt-3 mb-0">Add candidate</h5>
          <p>Data will be saved to database.</p>
        </div>
        <div class="float-end mt-3">
          <button
            @click="show = !show"
            class="btn btn-link text-dark p-0 fixed-plugin-close-button"
          >
            <i class="fa fa-close"></i>
          </button>
        </div>
      </div>
      <hr class="horizontal dark my-1" />
      <div class="card-body pt-sm-2 pt-0">
        <form action="">
          <div>
            <h6 class="mb-0">Details</h6>
          </div>
          <div class="form-group mb-1">
            <label for="example-text-input" class="form-control-label"
              >Name</label
            >
            <input
              class="form-control"
              type="text"
              value="John Snow"
              id="example-text-input"
              placeholder="Williams Shakespare"
            />
          </div>
          <div class="form-group mb-1">
            <label for="example-search-input" class="form-control-label"
              >Title</label
            >
            <input
              class="form-control"
              type="text"
              id="example-search-input"
              placeholder="Web developer"
            />
          </div>
          <div class="form-group mb-1">
            <label for="example-email-input" class="form-control-label"
              >Email</label
            >
            <input
              class="form-control"
              type="email"
              id="example-email-input"
            />
          </div>
          <div class="form-group mb-1">
            <label for="url" class="form-control-label"
              >Image url</label
            >
            <input
              class="form-control"
              type="text"
              id="url"
            />
          </div>
          <div class="mt-2">
            <h6 class="mb-0">Skills</h6>
          </div>
          <div class="d-fflex mb-1">
            <span class="badge badge-pill bg-primary">vue</span>
          </div>
          <div class=" d-flex">
            <div >
              <input
              class="form-control"
              type="text"
            />
            </div>
            <button @click="addSkill()" class="btn ms-3 btn-primary">
              <i class="fas fa-plus"></i>
            </button>
          </div>

          <div class="mt-3">
            <h6 class="mb-0">Social</h6>
          </div>
          <div class="form-group mb-1">
            <label for="Github" class="form-control-label"
              >Github</label
            >
            <input
              class="form-control"
              type="text"
              id="Github"
            />
          </div>

          <div class="form-group mb-1">
            <label for="LinkedIn" class="form-control-label"
              >LinkedIn</label
            >
            <input
              class="form-control"
              type="text"
              id="LinkedIn"
            />
          </div>
         
          <hr class="horizontal dark mb-1 d-xl-block d-none" />

          <hr class="horizontal dark my-sm-4" />
          <button class="btn btn-dark w-100">Submit</button>
        </form>
      </div>
    </div>
  </div>
</template>
