<template>
  <div>
    <div class="page-header pr-0">
      <h2>
        <a href="/hotels/floors">
          <svg  xmlns="http://www.w3.org/2000/svg" style="margin-top: -5px;" width="24"  height="24"  viewBox="0 0 24 24"  fill="none"  stroke="currentColor"  stroke-width="2"  stroke-linecap="round"  stroke-linejoin="round"  class="icon icon-tabler icons-tabler-outline icon-tabler-building-skyscraper"><path stroke="none" d="M0 0h24v24H0z" fill="none"/><path d="M3 21l18 0" /><path d="M5 21v-14l8 -4v18" /><path d="M19 21v-10l-6 -4" /><path d="M9 9l0 .01" /><path d="M9 12l0 .01" /><path d="M9 15l0 .01" /><path d="M9 18l0 .01" /></svg>
        </a>
      </h2>
      <ol class="breadcrumbs">
        <li class="active"><span>REGISTRO DE PISOS</span></li>
      </ol>
      <div class="right-wrapper pull-right">
        <div class="btn-group flex-wrap">
          <button
            type="button"
            class="btn btn-custom btn-sm mt-2 mr-2"
            @click="onCreate"
          >
            <i class="fa fa-plus-circle"></i> Nuevo
          </button>
        </div>
      </div>
    </div>
    <div class="card tab-content-default row-new mb-0">
      <!-- <div class="card-header bg-info">
        <h3 class="my-0">Listado de pisos</h3>
      </div> -->
      <div class="card-body">
        <div class="table-responsive">
          <table class="table">
            <thead>
              <tr>
                <th class="text-center">#</th>
                <th>Piso</th>
                <th class="text-center">Visible</th>
                <th></th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="item in items" :key="item.id">
                <td class="text-center">{{ item.id }}</td>
                <td>{{ item.description }}</td>
                <td class="text-center">
                  <span v-if="item.active">Si</span>
                  <span v-else>No</span>
                </td>
                <td class="text-center">
                  <el-button type="success" @click="onEdit(item)">
                    <i class="fa fa-edit"></i>
                  </el-button>
                  <el-button type="danger" @click="onDelete(item)">
                    <i class="fa fa-trash"></i>
                  </el-button>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
    <ModalAddEdit
      :visible.sync="openModalAddEdit"
      @onAddItem="onAddItem"
      @onUpdateItem="onUpdateItem"
      :floor="floor"
    ></ModalAddEdit>
  </div>
</template>

<script>
import ModalAddEdit from "./AddEdit";

export default {
  props: {
    floors: {
      type: Array,
      required: true,
    },
  },
  components: {
    ModalAddEdit,
  },
  data() {
    return {
      items: [],
      floor: null,
      openModalAddEdit: false,
      loading: false,
    };
  },
  mounted() {
    this.items = this.floors;
  },
  methods: {
    onDelete(item) {
      this.$confirm(`¿estás seguro de eliminar al elemento ${item.description}?`, 'Atención', {
          confirmButtonText: 'Si, continuar',
          cancelButtonText: 'No, cerrar',
          type: 'warning'
        }).then(() => {
          this.$http.delete(`/hotels/floors/${item.id}/delete`).then(response => {
            this.$message({
              type: 'success',
              message: response.data.message
            });
            this.items = this.items.filter(i => i.id !== item.id);
          }).catch(error => {
            this.axiosError(error)
          });
        }).catch();
    },
    onEdit(item) {
      this.floor = { ...item };
      this.openModalAddEdit = true;
    },
    onUpdateItem(data) {
      this.items = this.items.map((i) => {
        if (i.id === data.id) {
          return data;
        }
        return i;
      });
    },
    onAddItem(data) {
      this.items.unshift(data);
    },
    onCreate() {
      this.floor = null;
      this.openModalAddEdit = true;
    },
  },
};
</script>
