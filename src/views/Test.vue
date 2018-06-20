/* eslint-disable */
<template>
  <div class="test" >
    <ul class='list-group'>
      <draggable  v-model="data" :move="onMoveCallback" @start="drag=true" @end="drag=false">
        <!-- <li @click="folderOpen" v-for="item in data" class="list-group-item" :key="item.id"   > 
           {{item.name}}    -->
          <!-- <div> -->
          <!-- <ul  v-for="child in item.childrens" :key="child.id">  -->
            <Folder v-for="item in data" :key="item.id" v-bind:obj="item" v-bind:data-id="item.id"/>
             <!-- </ul> -->
          <!-- </div> -->
            <!-- </li> -->
      </draggable>
    </ul>
   <form @submit="addFolder">
   <input type="text" placeholder="Add your folder" v-model="newFolderName">
  <button type="submit">ADD+</button>
  </form> 

  </div>
</template>



<script>
/* eslint-disable */
import axios from "axios";
import draggable from "vuedraggable";
import Folder from "./Folders";
export default {
  name: "test",
  components: {
    draggable,
    Folder
  },
  data() {
    return {
      data: [],
      newFolderName: "",
      folderShow: false,
      currentOpen: null
    };
  },
  methods: {
    addFolder: function(e) {
      e.preventDefault();
      axios
        .post("http://albumbugs.test/api-medias/get-create-folder-child", {
          folder_id: "1",
          folder_name: this.newFolderName
        })
        .then(res => {
          this.data.push(res.data.data);
          console.log(res);
        })
        .catch(err => console.log(err));
    },
    onMoveCallback: function(evt) {
      console.log(evt);
      // console.log(evt.dragged);
      // console.log(evt.draggedRect);
      // console.log(evt.related);
      // console.log(evt.relatedRect);
      // console.log(evt.newIndex);
      // console.log(evt.oldIndex);
      console.log(evt.relatedContext.index);
      console.log(evt.draggedContext.index);
    },
    folderOpen: function(e) {
      console.log(e);
      let id = e.target.getAttribute("data-id");
      axios
        .post("http://albumbugs.test/api-medias/get-folder-childs", {
          folder_id: id
        })
        .then(res => {
          for (let i = 0; i < this.data.length; i++) {
            if (this.data[i].id == id) {
              this.$set(this.data[i], "childrens", res.data.data.childs);
              // this.data[i].childrens = res.data.data.childs;
              break;
            }
          }
          this.currentOpen = e.target;
          this.folderShow = true;
          console.log(this.data);
        })
        .catch(err => console.log(err));
    }
  },
  beforeCreate: function() {
    axios
      .post("http://albumbugs.test/api-medias/jstree", { folder_id: "1" })
      .then(res => {
        this.data = res.data.children;
      })
      .catch(err => console.log(err));
  },
  mounted: function() {
    console.log(this.data);
    // for (let i = 0; i < this.data.children.length; i++) {
    //   console.log(this.data.children[i].id);
    // }
    // this.mounted = true;
  }
};
</script>


<style>
ul {
  list-style: none;
}
</style>
