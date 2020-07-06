<template>
  <div>
    <div ref='viewer'></div>
    <!-- <template v-for="annot in annotations">
      <div class="dialog" :id="`dialog-${annot.id}`" :key="annot.id">
        <div
          class="dialog-anchor"
          :id="`dialog-${annot.id}-anchor`"
          slot="header"
        >
          <span>Click here to drag</span>
          <span class="clickable" @click="toggleDialog(`dialog-${annot.id}`)">
            X
          </span>
        </div>
        <!-- <el-main> -->
        <p>{{ annot }}</p>
        <!-- </el-main> -->
      </div>
    </template> -->
  </div>
</template>

<script>
/* eslint-disable */
import WebViewer from '@pdftron/webviewer'

export default {
  name: 'WebViewer',
  props: {
    path: String,
    url: String
  },
  data: function(){
        return {
      annotations: [
        { page: 1, x: 40, y: 40, w: 80, h: 40, id: "1" },
        { page: 1, x: 80, y: 80, w: 80, h: 40, id: "2" },
        { page: 1, x: 120, y: 120, w: 80, h: 40, id: "3" },
      ],
    };
  },
  mounted: function () {
        WebViewer({
            path: "../../node_modules/@pdftron/webviewer/public/",
            initialDoc: this.url, // replace with your own PDF file
          }, this.$refs.viewer).then((instance) => {
            // call apis here
            const { docViewer, annotManager, Annotations } = instance;
            instance.disableElements(["searchButton"]);
            instance.setTheme("dark");
            docViewer.setMargin(20);
            instance.disableElements(["annotationPopup", "contextMenuPopup"]);
            docViewer.on("documentLoaded", () => {
              for (let i = 0; i < this.annotations.length; i++) {
                let annot = this.annotations[i];
                const rectangleAnnot = new Annotations.RectangleAnnotation();
                rectangleAnnot.PageNumber = annot.page;
                rectangleAnnot.X = annot.x;
                rectangleAnnot.Y = annot.y;
                rectangleAnnot.Width = annot.w;
                rectangleAnnot.Height = annot.h;
                rectangleAnnot.Author = annotManager.getCurrentUser();
                rectangleAnnot.FillColor = new Annotations.Color(200, 0, 0, 0.4);
                rectangleAnnot.setCustomData("id", annot.id);
                annotManager.addAnnotation(rectangleAnnot);
                annotManager.redrawAnnotation(rectangleAnnot);
              }
            });
          });
    }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
div {
  width: 100%; 
  height: 100vh;
}
</style>
