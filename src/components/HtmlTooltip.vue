<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue';

const text =
  '{"__name__":"container_cpu_cfs_periods_total","container_label_architecture":"x86_64","container_label_build_date":"2023-03-28T04:02:10","container_label_com_docker_compose_config_hash":"025049b9a8b33998e9a6bf25ecd571a24464655bcd5437a8f215adbef46412e1","container_label_com_docker_compose_container_number":"1","container_label_com_docker_compose_depends_on":"receiver:service_started:false","container_label_com_docker_compose_image":"sha256:5accd71f2ba0f58eb51b6df581ef7a57c3bc5fb092ff791e8070fc0beeda78ad","container_label_com_docker_compose_oneoff":"False","container_label_com_docker_compose_project":"exemone","container_label_com_docker_compose_project_config_files":"/home/exemone/exemone/installer/exemone/docker-compose.yml","container_label_com_docker_compose_project_environment_file":"/home/exemone/exemone/installer/exemone/.env","container_label_com_docker_compose_project_working_dir":"/home/exemone/exemone/installer/exemone","container_label_com_docker_compose_service":"db-agent","container_label_com_docker_compose_version":"2.27.0","container_label_com_redhat_component":"ubi8-container","container_label_com_redhat_license_terms":"https://www.redhat.com/en/about/red-hat-end-user-license-agreements#UBI","container_label_description":"The Universal Base Image is designed and engineered to be the base layer for all of your containerized applications, middleware and utilities. This base image is freely redistributable, but Red Hat only supports Red Hat technologies through subscriptions for Red Hat products. This image is maintained by Red Hat and updated regularly.","container_label_distribution_scope":"public","container_label_io_buildah_version":"1.27.3","container_label_io_exem":"true","container_label_io_exem_one_app":"db-agent","container_label_io_exem_one_type":"application","container_label_io_exem_prod":"exemone","container_label_io_k8s_description":"The Universal Base Image is designed and engineered to be the base layer for all of your containerized applications, middleware and utilities. This base image is freely redistributable, but Red Hat only supports Red Hat technologies through subscriptions for Red Hat products. This image is maintained by Red Hat and updated regularly.","container_label_io_k8s_display_name":"Red Hat Universal Base Image 8","container_label_io_openshift_tags":"base rhel8","container_label_maintainer":"Red Hat, Inc.","container_label_name":"ubi8","container_label_release":"1112","container_label_restartcount":"4","container_label_summary":"Provides the latest release of Red Hat Universal Base Image 8.","container_label_url":"https://access.redhat.com/containers/#/registry.access.redhat.com/ubi8/images/8.7-1112","container_label_vcs_ref":"a995512a05037e3b60bbb1bf9fa6e394063131c3","container_label_vcs_type":"git","container_label_vendor":"Red Hat, Inc.","container_label_version":"8.7","id":"/docker/1c5f7ec4874ad2ec70a9c30501e03afc0fa7e6b1f66c5427100cc0d18cced26f","image":"exemone-db-agent","instance":"10.10.43.71:9200","job":"demo3","name":"exemone-db-agent","server":"demo3"}';
const textArr = Array.from({ length: 20 }).map(() => {
  return text;
});

const triggerArea = ref<HTMLDivElement | null>(null);
const tooltip = ref<HTMLDivElement | null>(null);
const mousePosition = ref({ x: 0, y: 0 });
const isHovering = ref(false);

const updateTooltipPosition = () => {
  if (!tooltip.value) return;

  const x = mousePosition.value.x + 15;
  const y = mousePosition.value.y + 15;

  tooltip.value.style.top = `${y}px`;
  tooltip.value.style.left = `${x}px`;
};

const handleMouseMove = (e: MouseEvent) => {
  mousePosition.value = {
    x: e.clientX,
    y: e.clientY,
  };
  updateTooltipPosition();
};

const handleMouseEnter = () => {
  isHovering.value = true;
  updateTooltipPosition();
};

const handleMouseLeave = () => {
  isHovering.value = false;
};

onMounted(() => {
  window.addEventListener('resize', updateTooltipPosition);
});

onUnmounted(() => {
  window.removeEventListener('resize', updateTooltipPosition);
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
    HTML 여기에 마우스 올리면 됨.
  </div>

  <div v-show="isHovering" ref="tooltip" class="tooltip">
    <ul class="tooltip-list">
      <li v-for="(item, index) in textArr" :key="index" class="tooltip-item">
        {{ index + 1 }}. {{ item }}
      </li>
    </ul>
  </div>
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

.tooltip {
  position: fixed;
  z-index: 999;
  background-color: rgba(0, 0, 0, 0.8);
  border-radius: 4px;
  padding: 10px;
  overflow-y: visible;
}

.tooltip-list {
  list-style-type: none;
  margin: 0;
  padding: 0;
}

.tooltip-item {
  color: #fff;
  font-size: 14px;
  padding: 5px 0;
  white-space: nowrap;
}
</style>
