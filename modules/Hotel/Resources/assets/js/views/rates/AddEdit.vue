<template>
  <el-dialog
    :title="title"
    :visible="visible"
    @close="onClose"
    @open="onCreate"
    width="400px"
  >
    <form autocomplete="off" @submit.prevent="onSubmit">
      <div class="form-body">
        <div class="form-group form-modern">
          <label class="control-label" for="description">Descripción</label>
          <el-input
            type="text"
            id="description"
            v-model="form.description"
            :class="{ 'is-invalid': errors.description }"
          />
          <div v-if="errors.description" class="invalid-feedback">
            {{ errors.description[0] }}
          </div>
        </div>
        <div class="form-group">
          <label>Mostrar tarifa</label>
          <el-switch v-model="form.active"></el-switch>
        </div>
        <div class="row text-center">
          <div class="col-6">
            <el-button
              native-type="submit"
              :disabled="loading"
              type="primary"
              class="btn-block"
              :loading="loading"
              >Guardar</el-button
            >
          </div>
          <div class="col-6">
            <el-button class="btn-block second-buton" @click="onClose">Cancelar</el-button>
          </div>
        </div>
      </div>
    </form>
  </el-dialog>
</template>

<script>
export default {
  props: {
    visible: {
      type: Boolean,
      required: true,
      default: false,
    },
    rate: {
      type: Object,
      required: false,
      default: {},
    },
  },
  data() {
    return {
      form: {},
      title: "",
      errors: {},
      loading: false,
    };
  },
  methods: {
    onUpdate() {
      this.loading = true;
      this.$http
        .put(`/hotels/rates/${this.rate.id}/update`, this.form)
        .then((response) => {
          this.$emit("onUpdateItem", response.data.data);
          this.onClose();
        })
        .finally(() => {
          this.loading = false;
          this.errors = {};
        })
        .catch((error) => {
          this.axiosError(error);
        });
    },
    onStore() {
      this.loading = true;
      this.$http
        .post("/hotels/rates/store", this.form)
        .then((response) => {
          this.$emit("onAddItem", response.data.data);
          this.onClose();
        })
        .finally(() => {
          this.loading = false;
          this.errors = {};
        })
        .catch((error) => {
          this.axiosError(error);
        });
    },
    onSubmit() {
      if (this.rate) {
        this.onUpdate();
      } else {
        this.onStore();
      }
    },
    onClose() {
      this.$emit("update:visible", false);
    },
    onCreate() {
      if (this.rate) {
        this.form = this.rate;
        this.title = "Editar tarifa";
      } else {
        this.title = "Crear tarifa";
        this.form = {
          active: true,
        };
      }
    },
  },
};
</script>
