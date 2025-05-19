<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue';

const text =
  '{"__name__":"container_cpu_cfs_periods_total","container_label_architecture":"x86_64","container_label_build_date":"2023-03-28T04:02:10","container_label_com_docker_compose_config_hash":"025049b9a8b33998e9a6bf25ecd571a24464655bcd5437a8f215adbef46412e1","container_label_com_docker_compose_container_number":"1","container_label_com_docker_compose_depends_on":"receiver:service_started:false","container_label_com_docker_compose_image":"sha256:5accd71f2ba0f58eb51b6df581ef7a57c3bc5fb092ff791e8070fc0beeda78ad","container_label_com_docker_compose_oneoff":"False","container_label_com_docker_compose_project":"exemone","container_label_com_docker_compose_project_config_files":"/home/exemone/exemone/installer/exemone/docker-compose.yml","container_label_com_docker_compose_project_environment_file":"/home/exemone/exemone/installer/exemone/.env","container_label_com_docker_compose_project_working_dir":"/home/exemone/exemone/installer/exemone","container_label_com_docker_compose_service":"db-agent","container_label_com_docker_compose_version":"2.27.0","container_label_com_redhat_component":"ubi8-container","container_label_com_redhat_license_terms":"https://www.redhat.com/en/about/red-hat-end-user-license-agreements#UBI","container_label_description":"The Universal Base Image is designed and engineered to be the base layer for all of your containerized applications, middleware and utilities. This base image is freely redistributable, but Red Hat only supports Red Hat technologies through subscriptions for Red Hat products. This image is maintained by Red Hat and updated regularly.","container_label_distribution_scope":"public","container_label_io_buildah_version":"1.27.3","container_label_io_exem":"true","container_label_io_exem_one_app":"db-agent","container_label_io_exem_one_type":"application","container_label_io_exem_prod":"exemone","container_label_io_k8s_description":"The Universal Base Image is designed and engineered to be the base layer for all of your containerized applications, middleware and utilities. This base image is freely redistributable, but Red Hat only supports Red Hat technologies through subscriptions for Red Hat products. This image is maintained by Red Hat and updated regularly.","container_label_io_k8s_display_name":"Red Hat Universal Base Image 8","container_label_io_openshift_tags":"base rhel8","container_label_maintainer":"Red Hat, Inc.","container_label_name":"ubi8","container_label_release":"1112","container_label_restartcount":"4","container_label_summary":"Provides the latest release of Red Hat Universal Base Image 8.","container_label_url":"https://access.redhat.com/containers/#/registry.access.redhat.com/ubi8/images/8.7-1112","container_label_vcs_ref":"a995512a05037e3b60bbb1bf9fa6e394063131c3","container_label_vcs_type":"git","container_label_vendor":"Red Hat, Inc.","container_label_version":"8.7","id":"/docker/1c5f7ec4874ad2ec70a9c30501e03afc0fa7e6b1f66c5427100cc0d18cced26f","image":"exemone-db-agent","instance":"10.10.43.71:9200","job":"demo3","name":"exemone-db-agent","server":"demo3"}';
const textArr = Array.from({ length: 20 }).map(() => {
  return text;
});

const canvas = ref<HTMLCanvasElement | null>(null);
const triggerArea = ref<HTMLDivElement | null>(null);
const mousePosition = ref({ x: 0, y: 0 });
const isHovering = ref(false);
const pixelRatio = ref(2);
const tooltipWidth = ref(0);
const tooltipHeight = ref(0);

const calculateTooltipSize = () => {
  if (!canvas.value) return { width: 0, height: 0 };

  const ctx = canvas.value.getContext('2d');
  if (!ctx) return { width: 0, height: 0 };

  ctx.font = '14px Arial';

  let maxWidth = 0;
  textArr.forEach((text, index) => {
    const definedText = `${index + 1}. ${text}`;
    const textWidth = ctx.measureText(definedText).width;
    maxWidth = Math.max(maxWidth, textWidth);
  });

  const padding = 20;
  const width = maxWidth + padding * 2;
  const height = 20 * textArr.length + padding * 2;

  return { width, height };
};

const drawText = () => {
  if (!canvas.value) return;

  const ctx = canvas.value.getContext('2d');
  if (!ctx) return;

  const { width, height } = calculateTooltipSize();
  tooltipWidth.value = width;
  tooltipHeight.value = height;

  canvas.value.width = width * pixelRatio.value;
  canvas.value.height = height * pixelRatio.value;

  canvas.value.style.width = `${width}px`;
  canvas.value.style.height = `${height}px`;

  const x = mousePosition.value.x + 15;
  const y = mousePosition.value.y + 15;
  canvas.value.style.top = `${y}px`;
  canvas.value.style.left = `${x}px`;

  ctx.scale(pixelRatio.value, pixelRatio.value);
  ctx.clearRect(0, 0, width, height);

  if (isHovering.value) {
    ctx.font = '14px Arial';
    ctx.fillStyle = '#333';
    ctx.fillRect(0, 0, width, height);
    ctx.fillStyle = '#fff';

    const padding = 10;
    textArr.forEach((text, index) => {
      const definedText = `${ctx.measureText(text).width} ${text}`;

      ctx.fillText(`${index + 1}. ${definedText}`, padding, padding + index * 20);
    });
  }
};

const handleMouseMove = (e: MouseEvent) => {
  mousePosition.value = {
    x: e.clientX,
    y: e.clientY,
  };
  drawText();
};

const handleMouseEnter = () => {
  isHovering.value = true;
  drawText();
};

const handleMouseLeave = () => {
  isHovering.value = false;
  if (canvas.value) {
    const ctx = canvas.value.getContext('2d');
    if (ctx) {
      ctx.clearRect(0, 0, canvas.value.width, canvas.value.height);
    }
  }
};

onMounted(() => {
  window.addEventListener('resize', drawText);
});

onUnmounted(() => {
  window.removeEventListener('resize', drawText);
});
</script>

<template>
  <div
    ref="triggerArea"
    class="trigger-area"
    @mousemove="handleMouseMove"
    @mouseenter="handleMouseEnter"
    @mouseleave="handleMouseLeave"
  >
    Canvas 여기에 마우스 올리면 됨.
  </div>
  <canvas v-show="isHovering" ref="canvas" class="canvas"></canvas>
</template>

<style scoped>
.trigger-area {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 400px;
  height: 400px;
  border: 1px solid #ff0000;
  cursor: pointer;
}

.canvas {
  position: absolute;
  z-index: 999;
  background-color: rgba(0, 0, 0, 0.8);
  border-radius: 4px;
}
</style>
