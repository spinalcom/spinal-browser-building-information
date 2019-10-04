
<template>
  <v-container grid-list-md
               class="containerSize">
    <v-layout wrap
              column
              class="vertical-center">
      <v-container>
        <h1 class="my-text-center">{{display.Titre}}</h1>
        <h2 class="my-text-center">{{display.Bâtiment}}</h2>
        <h3 class="my-text-center">{{display.Adresse}}</h3>
        <h3 class="my-text-center">{{display.Ville}}</h3>
        <h3 class="my-text-center">{{display.Surface}}</h3>
      </v-container>
    </v-layout>
  </v-container>
</template>

<script>
import { appService } from "spinal-env-viewer-plugin-apps-generator/service/appsService";
import { SpinalGraphService } from "spinal-env-viewer-graph-service";
import { spinalIO } from "../../config/spinal-io";
import { serviceDocumentation } from "spinal-env-viewer-plugin-documentation-service";
import { getParameterByName } from "../../config/spinal-io";
import { BUILDING_TYPE } from "spinal-env-viewer-context-geographic-service/build/constants";

export default {
  name: "appstore",
  data() {
    return {
      show: false,
      informationList: [],
      idContext: "",
      selectedNode: undefined,
      display: {
        Titre: "",
        Bâtiment: "",
        Adresse: "",
        Ville: "",
        Surface: ""
      }
    };
  },
  props: [],
  components: {},
  methods: {
    findBuildingNode(idContext) {
      let contextNode = SpinalGraphService.getRealNode(idContext);
      let buildingFinded = contextNode
        .findInContext(contextNode, node => {
          if (node.info.type.get() == BUILDING_TYPE) {
            return true;
          }
        })
        .then(buildingFinded => {
          serviceDocumentation
            .setBuildingInformationAttributes(buildingFinded[0])
            .then(info => {
              this.informationList = info;
              console.log(info);
              for (let i = 0; i < info.length; i++) {
                const element = info[i];
                this.display[element.label.get()] = element.value.get();
              }
            });
        });
    }
  },
  mounted() {
    let user = spinalIO.getauth();
    this.selectedNode = undefined;
    this.idContext = getParameterByName("node");
    this.findBuildingNode(this.idContext);
  },
  computed: {
    getStyle() {}
  }
};
</script>

<style >
.sizecard {
  margin: 10px !important;
}
.inlineblockTitle {
  width: 100%;
  text-align: center;
  margin-bottom: 15px;
}
.alignTitle {
  width: calc(100% - 70px);
  display: inline-block;
  margin-left: 13px;
}
.containerSize {
  width: 100%;
  height: 100%;
}
.vertical-center {
  margin: 0;
  position: absolute;
  top: 50%;
  -ms-transform: translateY(-50%);
  transform: translateY(-50%);
}
.breakText {
  word-break: break-word;
}
@media screen and (min-width: 960px) {
  .siezFlexBox {
    max-width: 200px !important;
    min-width: 200px !important;
  }
}
@media screen and (max-width: 960px) {
  .siezFlexBox {
    max-width: 200px !important;
    min-width: 200px !important;
  }
}
.my-text-center {
  text-align: center;
  margin: 20px;
}
</style>
