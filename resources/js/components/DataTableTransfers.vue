<template>
  <div>
    <div class="row">
      <div class="col-md-12 col-lg-12 col-xl-12">
        <el-button
          type="primary"
          class="btn-show-filter mb-2"
          :class="{ shift: isVisible }"
          @click="toggleInformation"
        >
          {{ isVisible ? "Ocultar opciones de filtro" : "Mostrar opciones de filtro" }}
      </el-button>
        <div class="row" v-if="isVisible">
          <div class="col-lg-4 col-md-4 col-sm-12 pb-2">
            <div class="d-flex">
              <div style="width:100px">Filtrar por:</div>
              <el-select v-model="search.column" placeholder="Select" @change="changeClearInput">
                <el-option v-for="(label, key) in columns" :key="key" :value="key" :label="label"></el-option>
              </el-select>
            </div>
          </div>
          <div class="col-lg-3 col-md-4 col-sm-12 pb-2">
            <template
              v-if="search.column=='created_at'"
            >
              <el-date-picker
                v-model="search.value"
                type="date"
                style="width: 100%;"
                placeholder="Buscar"
                value-format="yyyy-MM-dd"
                @change="getRecords"
              ></el-date-picker>
            </template>
          </div>
        </div>
      </div>

      <div class="col-md-12">
        <div class="table-responsive">
          <table class="table">
            <thead>
              <slot name="heading"></slot>
            </thead>
            <tbody>
              <slot v-for="(row, index) in records" :row="row" :index="customIndex(index)"></slot>
            </tbody>
          </table>
          <div>
            <el-pagination
              @current-change="getRecords"
              layout="total, prev, pager, next"
              :total="pagination.total"
              :current-page.sync="pagination.current_page"
              :page-size="pagination.per_page"
            ></el-pagination>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>


<script>
import moment from "moment";
import queryString from "query-string";

export default {
  props: {
    resource: String
  },
  data() {
    return {
      search: {
        column: null,
        value: null
      },
      columns: [],
      records: [],
      isVisible: false,
      pagination: {}
    };
  },
  computed: {},
  created() {
    this.$eventHub.$on("reloadData", () => {
      this.getRecords();
    });
  },
  async mounted() {
    let column_resource = _.split(this.resource, "/");
    // console.log(column_resource)
    await this.$http
      .get(`/${_.head(column_resource)}/columns`)
      .then(response => {
        this.columns = response.data;
        this.search.column = _.head(Object.keys(this.columns));
      });
    await this.getRecords();
  },
  methods: {
    toggleInformation() {
      this.isVisible = !this.isVisible;
    },
    customIndex(index) {
      return (
        this.pagination.per_page * (this.pagination.current_page - 1) +
        index +
        1
      );
    },
    getRecords() {
      return this.$http
        .get(`/${this.resource}/records?${this.getQueryParameters()}`)
        .then(response => {
          this.records = response.data.data;
          this.pagination = response.data.meta;
          this.pagination.per_page = parseInt(response.data.meta.per_page);
        });
    },
    getQueryParameters() {
      return queryString.stringify({
        page: this.pagination.current_page,
        limit: this.limit,
        ...this.search
      });
    },
    changeClearInput() {
      this.search.value = "";
      this.getRecords();
    }
  }
};
</script>
