<template>
  <div class="pa-3">
    <h3>{{ course.name }}</h3>
    <v-select
      v-model="currentIndex"
      :items="course.episodes.map((v, i) => ({ text: v.name, value: i }))"
    ></v-select>
    <!-- <p>{{ episode }}</p> -->
    <video :src="episode.file" width="100%" controls></video>
    <!-- <v-img
      :src="episode.file"
      :lazy-src="episode.file"
      aspect-ratio="2"
      max-width="500"
      max-height="300"
    ></v-img> -->
  </div>
</template>

<script>
export default {
  data() {
    return {
      currentIndex: 0
    }
  },
  computed: {
    episode() {
      return this.course.episodes[this.currentIndex]
    }
  },
  async asyncData({ params, $axios }) {
    const { id } = params
    const course = await $axios.$get(`courses/${id}`, {
      params: {
        query: { populate: 'episodes' }
      }
    })
    return {
      id,
      course
    }
  }
}
</script>

<style></style>
