<template>
    <div class="container">
      <br>
      <form enctype="multipart/form-data">

        <input  accept=".csv" @change="filesChange($event.target.name, $event.target.files)" class="form-control form-control-lg" id="formFileLg" type="file" />
        <label for="formFileLg" class="form-label">Seleccione archivo con datos e inmediatamente será cargado</label>

      </form>
      <br>
      <table class="table table-striped">
        <thead>
        <tr class="text-center">
          <th>Nombre</th>
          <th class="text-center">Model 1</th>
          <th class="text-center">Model 2</th>
          <th class="text-center">Model 3</th>
        </tr>
        </thead>
        <tbody>
        <tr v-for="p in displayedPredictions" :key="p" class="text-center">
          <td>{{p.nombre}}</td>
          <td>{{p.model1}}</td>
          <td>{{p.model2}}</td>
          <td>{{p.model3}}</td>

        </tr>
        </tbody>
      </table>
      <nav aria-label="Page navigation example">
        <ul class="pagination">
          <li class="page-item">
            <button type="button" class="page-link" v-if="page != 1" @click="page--"> Previous </button>
          </li>
          <li class="page-item">
            <button type="button" class="page-link" v-for="pageNumber in pages.slice(page-1, page+5)" :key="pageNumber" @click="page = pageNumber"> {{pageNumber}} </button>
          </li>
          <li class="page-item">
            <button type="button" @click="page++" v-if="page < pages.length" class="page-link"> Next </button>
          </li>
        </ul>
      </nav>
    </div>
</template>

<script>
import { upload } from '../Services/api';

export default {
name: "Form",
  data(){
    return {
      predictions:[],
      page:1,
      perPage:10,
      pages:[]
    }
  },
  methods:{

    filesChange(fieldName, fileList) {
      const formData = new FormData();
      if (!fileList.length) return;

      formData.append('file',fileList[0])

      upload(formData).then(response =>{
        this.predictions=response;
      })
    },
    setPages(){
      let numberOfPages = Math.ceil(this.predictions.length / this.perPage);
      for (let index = 1; index <= numberOfPages; index++) {
        this.pages.push(index);
    }},
    paginate(predictions){
      let page=this.page;
      let perPage=this.perPage;
      let from =(page*perPage)-perPage;
      let to=(page*perPage);
      console.log(predictions.slice(from,to))
      return predictions.slice(from,to);
    },
  },
  computed:{
    displayedPredictions(){
     return this.paginate(this.predictions);
    }
  },
  watch:{
    predictions(){
      this.setPages();
    }
  },
  filters:{
    trimWords(value){
      return value.split(" ").splice(0,20).join(" ") + '...';
    }
  }
}

</script>

<style scoped>
button.page-link {
  display: inline-block;
}
button.page-link {
  font-size: 20px;
  color: #29b3ed;
  font-weight: 500;
}
.offset{
  width: 500px !important;
  margin: 20px auto;
}
</style>
