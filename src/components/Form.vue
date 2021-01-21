<template>
  <div class="container">
    <br />
    <div class="searchTool">
      <form enctype="multipart/form-data" class="mx-2">
        <input
          accept=".csv"
          @change="filesChange($event.target.name, $event.target.files)"
          class="form-control"
          id="formFile"
          type="file"
        />
      </form>
      <a class="btn btn-primary mx-2" role="button" v-on:click="clean">
        <i class="fas fa-minus-circle"></i> Clean</a
      >
      <a class="btn btn-info mx-2" role="button" v-on:click="onClick">
        <i class="fas fa-cloud-download-alt"></i> Get example.csv</a
      >

    </div>
    <label for="formFileLg" class="form-label"
      >Seleccione archivo con datos e inmediatamente será cargado</label
    >
    <br>

    <input type="text" v-model="search" placeholder="Buscar por nombre" class="my-3" />

    <table class="table table-striped">
      <thead>
        <tr>
          <th>Nombre</th>
          <th>RNA MLP</th>
          <th>SVM</th>
          <th>Random Forest</th>
          <th>Moda</th>

        </tr>
      </thead>
      <tbody>
        <tr v-for="row in rows" :key="row.id">
          <td>{{ row.Name }}</td>
          <td>
              <i v-if="row.model1>0" class="fas fa-check-double"></i>
              <i v-else class="fas fa-skull-crossbones"></i>
          </td>

          <td>
            <i v-if="row.model2>0" class="fas fa-check-double"></i>
            <i v-else class="fas fa-skull-crossbones"></i>
          </td>

          <td>
            <i v-if="row.model3>0" class="fas fa-check-double"></i>
            <i v-else class="fas fa-skull-crossbones"></i>
          </td>
          <td>
            <i v-if="row.mode>0" class="fas fa-check-double"></i>
            <i v-else class="fas fa-skull-crossbones"></i>
          </td>
        </tr>
      </tbody>
    </table>

    <nav>
      <ul class="pagination" >
        <li class="page-item">
          <button
            type="button"
            class="page-link"
            v-if="page != 1"
            @click="page--"
          >
            Anterior
          </button>
        </li>
        <li class="page-item">
          <button
            type="button"
            class="page-link"
            v-for="pageNumber in pages.slice(page - 1, page + 5)"
            :key="pageNumber"
            @click="page = pageNumber"
          >
            {{ pageNumber }}
          </button>
        </li>
        <li class="page-item">
          <button
            type="button"
            @click="page++"
            v-if="page < pages.length"
            class="page-link"
          >
            Siguiente
          </button>
        </li>
      </ul>
    </nav>

  </div>
</template>

<script>
import { upload } from "../Services/api";
import { saveAs } from 'file-saver';

export default {
  name: "Form",
  data() {
    return {
      predictions: [],
      page: 1,
      perPage: 10,
      pages: [],
      search: null,
    };
  },
  methods: {
      onClick() {

        fetch(process.env.VUE_API+'/api/predict/example/', {
            headers: {
              'Content-Type': 'text/csv'
            },
            responseType: 'blob'
          }).then(response => response.blob())
            .then(blob => saveAs(blob, 'Example.csv'));
      },
    filesChange(fieldName, fileList) {
      const formData = new FormData();
      if (!fileList.length) return;

      formData.append("file", fileList[0]);

      upload(formData).then((response) => {
        this.predictions = response;
        console.log(response);
      });
    },
    setPages() {
      let numberOfPages = Math.ceil(this.predictions.length / this.perPage);
      for (let index = 1; index <= numberOfPages; index++) {
        this.pages.push(index);
      }
    },
    paginate(predictions) {
      let page = this.page;
      let perPage = this.perPage;
      let from = page * perPage - perPage;
      let to = page * perPage;
      return predictions.slice(from, to);
    },
    clean() {
      this.predictions = [];
      this.pages=[];
    },
  },
  computed: {
    displayedPredictions() {
      return this.paginate(this.predictions);
    },
    rows() {
      if (!this.predictions.length) {
        return [];
      }

      return this.paginate(
        this.predictions.filter((item) => {
          let props =
            this.search && this.name ? [item[name]] : Object.values(item);

          return props.some(
            (prop) =>
              !this.search ||
              (typeof prop === "string"
                ? prop.includes(this.search)
                : prop.toString(10).includes(this.search))
          );
        })
      );
    },
  },
  watch: {
    predictions() {
      this.setPages();
    },
  },
  filters: {
    trimWords(value) {
      return value.split(" ").splice(0, 20).join(" ") + "...";
    },
  },
};
</script>

<style scoped>
button.page-link {
  display: inline-block;
}
button.page-link {
  font-size: 16px;
  font-weight: normal;
  color: #1266f1;
}
.offset {
  width: 400px !important;
  margin: 20px auto;
}
a {
  font-size: 14px;
}
.searchTool {
  display: flex;
  justify-content: center;
  text-align: center;
  align-items: center;
}
table {
  justify-content: center;
margin: 0 auto;
  width: 80%;
}
th {
  font-weight: bold;
  font-size: 17px;
}
nav{
  width: 80%;
  margin: 0 auto;
}
</style>
