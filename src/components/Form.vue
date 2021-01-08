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
      <!-- <a class="btn btn-primary" href="" role="button">Call to action</a> -->
      <a class="btn btn-primary mx-2" role="button" v-on:click="clean">
        <i class="fas fa-minus-circle"></i> Limpiar</a
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
          <th>Model 1</th>
          <th>Model 2</th>
          <th>Model 3</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="row in rows" :key="row.id">
          <td>{{ row.nombre }}</td>
          <td>{{ row.model1 }}</td>
          <td>{{ row.model2 }}</td>
          <td>{{ row.model3 }}</td>
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
    filesChange(fieldName, fileList) {
      const formData = new FormData();
      if (!fileList.length) return;

      formData.append("file", fileList[0]);

      upload(formData).then((response) => {
        this.predictions = response;
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
      console.log(predictions.slice(from, to));
      return predictions.slice(from, to);
    },
    clean() {
      this.predictions = [];
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