<template class="folder">
<div> 
            <li  @click="onClick" v-bind:data-id="data.id">{{data.name}}</li>
            <ul v-if="test" >
                <p>test</p>
                <Folder v-for="item in data.childrens" :key="item.id" v-bind:obj="item" v-bind:data-id="item.id" />
              </ul>          
</div>
</template>

<script>
import Folder from "./Folders";
import axios from "axios";
export default {
  name: "folder",
  props: ["obj"],
  components: {
    Folder
  },
  data() {
    return {
      data: this.obj,
      test: false,
      newData: null
    };
  },
  methods: {
    onClick: function(e) {
      console.log(e);
      let id = e.target.getAttribute("data-id");
      console.log(id);
      axios
        .post("http://albumbugs.test/api-medias/get-folder-childs", {
          folder_id: id
        })
        .then(res => {
          this.$set(this.data, "childrens", res.data.data.childs);
          this.test = true;
        })
        .catch(err => console.log(err));
    }
  }
};
</script>

