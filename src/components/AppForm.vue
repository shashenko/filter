<script>
import AppMultiSelect from "@/components/AppMultiSelect.vue"

export default {
  name: "AppForm",
  components: {
    AppMultiSelect,
  },
  data() {
    return {
      project: [],
      category: [],
      categories: [
        {
          value: 1,
          label: 'Hobbies',
        },
        {
          value: 2,
          label: 'General',
        },
        {
          value: 3,
          label: 'Sports',
        },
        {
          value: 4,
          label: 'Art and Entertainment',
        },
        {
          value: 5,
          label: 'Internet and Telecom',
        },
        {
          value: 6,
          label: 'Games',
        },
        {
          value: 7,
          label: 'Electronics',
        },
        {
          value: 8,
          label: 'Outdoors',
        },
        {
          value: 9,
          label: 'Education',
        },
        {
          value: 10,
          label: 'Home and Garden',
        },
        {
          value: 11,
          label: 'Automotive',
        },
        {
          value: 12,
          label: 'Business and Industry',
        },
        {
          value: 13,
          label: 'Travel',
        },
        {
          value: 14,
          label: 'Food and Drink',
        },
        {
          value: 15,
          label: 'People and Society',
        },
      ],
      projects: [
        {
          value: 1,
          label: 'wisepick',
        },
        {
          value: 2,
          label: 'Talkaboutsleep',
        },
        {
          value: 3,
          label: 'Recroompick',
        },
        {
          value: 4,
          label: 'GrillGuru',
        },
        {
          value: 5,
          label: 'Outdoorsly',
        },
      ],
    }
  },
  methods: {
    onApply() {
      //this.$refs.form.submit();
    },
  },
}
</script>

<template>
  <div>
    <form class="app-form" ref="form">
      <app-multi-select :options="categories" placeholder="Category" v-model="category" @apply="onApply" />
      <app-multi-select :options="projects" placeholder="Project" v-model="project" @apply="onApply" />
    </form>
  </div>
</template>

<style lang="scss">
.app-form {
  display: flex;

  .app-multi-select {
    &:not(:last-child) {
      margin-right: 20px;
    }
  }
}
</style>