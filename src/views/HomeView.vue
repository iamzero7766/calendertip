<template>
  <div class="home">
    <calender-tip></calender-tip>
  </div>
</template>

<script>
// @ is an alias to /src
import CalenderTip from "@/components/CalenderTip.vue";

export default {
  name: "HomeView",
  components: {
    CalenderTip,
  },
};
</script>
