<template>
  <svg
    fill="none"
    viewBox="0 0 24 24"
    stroke="currentColor"
    aria-hidden="true"
  >
    <path
      stroke-linecap="round"
      stroke-linejoin="round"
      :stroke-width="strokeWidth"
      d="M6 18L18 6M6 6l12 12"
    />
  </svg>
</template>
<script>
export default {
  props: {
    strokeWidth: {
      default: 2,
    },
  },
};
</script>