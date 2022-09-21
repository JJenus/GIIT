<script setup>
const props = defineProps({
  candidate: {
    type: Object,
    required: true,
  },
});
</script>

<template>
  <div class="col-md-4 mt-4 candidates">
    <div class="card">
      <img height="200" class="card-img-top" :src="candidate.imgUrl" />
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
        <h4>{{ candidate.name }}</h4>
        <p>{{ candidate.jobTitle }}</p>
        <p>{{ candidate.email }}</p>
        <div class="d-flex flex-wrap mb-2">
          <span
            v-for="skill in candidate.skills"
            class="rounded-pill py-1 px-2 m-1 mb-0 border border-dark"
          >
            {{ skill }}
          </span>
        </div>
        <div class="d-flex align-items-center justify-content-center">
          <a :href="candidate.github" class="btn btn-github btn-icon m-2">
            <span class="btn-inner--icon"
              ><i class="fab fs-6 fa-github"></i
            ></span>
          </a>
          <a :href="candidate.linkedin" class="btn btn-linkedin btn-icon m-2">
            <span class="btn-inner--icon"
              ><i class="fab fs-6 fa-linkedin"></i
            ></span>
          </a>
        </div>
      </div>
    </div>
  </div>
</template>
