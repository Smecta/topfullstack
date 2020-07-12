<template>
  <div>
    <v-toolbar>
      <v-btn icon>
        <v-icon>mdi-export-variant</v-icon>
      </v-btn>
      <v-toolbar-title>北京xxxx有限责任公司</v-toolbar-title>

      <v-spacer></v-spacer>

      <v-toolbar-items>
        <v-btn text>主页</v-btn>
        <v-btn text>公司介绍</v-btn>
        <v-btn text>新闻资讯</v-btn>
        <v-btn text>产品展示</v-btn>
        <v-btn text>公司招聘</v-btn>
        <v-btn text>联系我们</v-btn>
      </v-toolbar-items>
      <!-- <template v-if="$vuetify.breakpoint.smAndUp">
        <v-btn icon>
          <v-icon>mdi-export-variant</v-icon>
        </v-btn>
        <v-btn icon>
          <v-icon>mdi-delete-circle</v-icon>
        </v-btn>
        <v-btn icon>
          <v-icon>mdi-plus-circle</v-icon>
        </v-btn>
      </template> -->
    </v-toolbar>
    <template>
      <v-carousel
        cycle
        height="400"
        hide-delimiter-background
        show-arrows-on-hover
      >
        <v-carousel-item v-for="(slide, i) in slides" :key="i">
          <v-sheet :color="colors[i]" height="100%">
            <v-row class="fill-height" align="center" justify="center">
              <div class="display-3">{{ slide }} Slide</div>
            </v-row>
          </v-sheet>
        </v-carousel-item>
      </v-carousel>
    </template>
  </div>
</template>

<script>
export default {
  data() {
    return {
      colors: [
        'indigo',
        'warning',
        'pink darken-2',
        'red lighten-1',
        'deep-purple accent-4'
      ],
      slides: ['First', 'Second', 'Third', 'Fourth', 'Fifth']
    }
  }
}
</script>
<style></style>
