<script setup>
import { onMounted, ref } from "vue";
import { globePoints } from "@/stores/globePoints.js";
import Candidate from "../component/Candidate.vue";
import axios from "axios";

const env = import.meta.env;

const store = globePoints();
const candidates = ref([]);

let shortList = ref(50);
let searchInput = ref('')

const skill = ref(null);

const form = ref({
  name: null,
  jobTitle: null,
  email: null,
  skills: ["vue", "angular", "Js"],
  linkedin: null,
  github: null,
  imgUrl: null,
});

onMounted(() => {
  init();
  fetchCandidates();
});

function fetchCandidates() {
  console.log(env.VITE_BE_API);
  const config = {
    method: "GET",
    url: env.VITE_BE_API + "/candidates",
  };

  axios
    .request(config)
    .then((res) => {
      console.log(res);
      candidates.value.push(...res.data);
    })
    .catch((err) => {
      console.log(err);
    });
}

function addSkill() {
  if (!skill.value) return;
  form.value.skills.push(skill.value);
  skill.value = null;
}

function removeSkill(index) {
  form.value.skills.splice(index, 1);
}

function saveCandidate() {
  const config = {
    method: "POST",
    url: env.VITE_BE_API + "/candidates",
    data: JSON.stringify(form.value),
    headers: {
      "Content-Type": "application/json",
    },
  };

  axios
    .request(config)
    .then((res) => {
      candidates.value.push(res.data);
      document.getElementById("close-btn").click();
    })
    .catch((err) => {
      console.log(err);
    });
}

function search() {
  const rjx = new RegExp(searchInput.value, 'i')
  document.querySelectorAll(".candidates").forEach((e) => {
    if(rjx.test(e.innerText)){
      e.style.display = "block";
    }
    else{
      e.style.display = "none";
    }
  });
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
      makeMagic(store.points);
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
        <nav
          class="d-flex align-items-end mb-3 mb-md-0"
          aria-label="breadcrumb"
        >
          <div>
            <img
              class="avatar"
              src="https://media-exp1.licdn.com/dms/image/C4D03AQHEtMgx8MzJHw/profile-displayphoto-shrink_800_800/0/1621511606507?e=1669248000&v=beta&t=FBNeZ9e4k1GdnY-EjsCydrPCgPmzvtET-KR3R_l8VSk"
              alt=""
            />
          </div>
          <h6 class="font-weight-bolder ms-3 mb-0">JJenus</h6>
        </nav>
        <form action="">
          <div class="input-group">
            <span class="input-group-text text-body"
              ><i class="fas fa-search" aria-hidden="true"></i
            ></span>
            <input
              @keyup="search()"
              v-model="searchInput"
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
                          {{ shortList }}
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
                        <h5 class="font-weight-bolder mb-0">
                          {{ candidates.length }}
                        </h5>
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
        <Candidate
          v-for="candidate in candidates"
          :candidate="candidate"
        ></Candidate>
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
    <button
      id="close-btn"
      type="button"
      class="btn d-none bg-gradient-primary"
      data-bs-toggle="modal"
      data-bs-target="#alert-success"
    >
      Launch demo modal
    </button>
    <div
      class="modal shpw fade"
      id="alert-success"
      tabindex="-1"
      role="dialog"
      aria-labelledby="alert-success"
      aria-hidden="true"
    >
      <div class="modal-dialog modal-dialog-centered" role="document">
        <div class="modal-content">
          <div class="modal-body">
            <button
              type="button"
              class="btn-close ms-auto position-absolute text-dark"
              data-bs-dismiss="modal"
              aria-label="Close"
            >
              <span aria-hidden="true">&times;</span>
            </button>

            <div class="margin-auto fs-3 text-center">
              <i class="fas fa-check-circle text-success me-2"></i> Saved
            </div>
          </div>
        </div>
      </div>
    </div>
  </main>

  <div :class="show ? 'show' : ''" class="fixed-plugin">
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
        <form action="" @submit.prevent="saveCandidate()">
          <div>
            <h6 class="mb-0">Details</h6>
          </div>
          <div class="form-group mb-1">
            <label for="example-text-input" class="form-control-label"
              >Name</label
            >
            <input
              v-model="form.name"
              class="form-control"
              type="text"
              id="example-text-input"
              placeholder="Williams Shakespare"
              required
            />
          </div>
          <div class="form-group mb-1">
            <label for="jt" class="form-control-label">Job Title</label>
            <input
              v-model="form.jobTitle"
              class="form-control"
              type="text"
              id="jt"
              placeholder="Web developer"
              required
            />
          </div>
          <div class="form-group mb-1">
            <label for="example-email-input" class="form-control-label"
              >Email</label
            >
            <input
              v-model="form.email"
              class="form-control"
              type="email"
              id="example-email-input"
              required
            />
          </div>
          <div class="form-group mb-1">
            <label for="url" class="form-control-label">Image url</label>
            <input
              v-model="form.imgUrl"
              class="form-control"
              type="text"
              id="url"
            />
          </div>
          <div class="mt-2">
            <h6 class="mb-0">Skills</h6>
          </div>
          <div class="d-flex mb-2 flex-wrap">
            <span
              v-for="(s, i) in form.skills"
              class="rounded-pill py-0 px-2 m-1 mb-0 border border-dark"
            >
              {{ s }} <i @click="removeSkill(i)" class="ms-2 fa fa-close"></i>
            </span>
          </div>
          <div class="d-flex">
            <div class="">
              <input
                v-model="skill"
                class="form-control"
                type="text"
                id="skills"
                @keyup.enter="addSkill()"
              />
            </div>
            <button
              type="button"
              @click="addSkill()"
              class="btn ms-3 btn-primary"
            >
              <i class="fas fa-plus"></i>
            </button>
          </div>

          <div class="mt-3">
            <h6 class="mb-0">Social</h6>
          </div>
          <div class="form-group mb-1">
            <label for="Github" class="form-control-label">Github</label>
            <input
              v-model="form.github"
              class="form-control"
              type="text"
              id="Github"
            />
          </div>

          <div class="form-group mb-1">
            <label for="LinkedIn" class="form-control-label">LinkedIn</label>
            <input
              v-model="form.linkedin"
              class="form-control"
              type="text"
              id="LinkedIn"
            />
          </div>

          <hr class="horizontal dark mb-1 d-xl-block d-none" />

          <hr class="horizontal dark my-sm-4" />
          <button type="submit" class="btn btn-dark w-100">Submit</button>
        </form>
      </div>
    </div>
  </div>
</template>
