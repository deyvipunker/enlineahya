<template>
    <el-dialog :title="titleDialog" :visible="showDialog" @close="close" @open="create">
        <form autocomplete="off" @submit.prevent="submit">
            <div class="form-body">
                <div class="row">
                    <div class="col-md-4">
                        <div class="form-group" :class="{'has-danger': errors.id}">
                            <label class="control-label">Código</label>
                            <el-input v-model="form.id" :readonly="recordId!=null" :maxlength="2"></el-input>
                            <small class="form-control-feedback" v-if="errors.id" v-text="errors.id[0]"></small>
                        </div>
                    </div>
                    <div class="col-md-8">
                        <div class="form-group" :class="{'has-danger': errors.description}">
                            <label class="control-label">Descripción</label>
                            <el-input v-model="form.description"></el-input>
                            <small class="form-control-feedback" v-if="errors.description" v-text="errors.description[0]"></small>
                        </div>
                    </div>
                </div>
            </div>
            <div class="form-actions text-right mt-4">
                <el-button class="second-buton" @click.prevent="close()">Cancelar</el-button>
                <el-button type="primary" native-type="submit" :loading="loading_submit">Guardar</el-button>
            </div>
        </form>
    </el-dialog>

</template>

<script>

    export default {
        props: ['showDialog', 'recordId','external'],
        data() {
            return {
                loading_submit: false,
                titleDialog: null,
                resource: 'card_brands',
                errors: {},
                form: {},
            }
        },
        created() {
            this.initForm()
        },
        methods: {
            initForm() {
                this.errors = {}
                this.form = {
                    id: null,
                    description: null,
                    is_update:false
                }
            },
            create() {
                this.titleDialog = (this.recordId)? 'Editar Tarjeta':'Nueva Tarjeta'
                if (this.recordId) {
                    this.$http.get(`/${this.resource}/record/${this.recordId}`)
                        .then(response => {
                            this.form = response.data
                            this.form.is_update = true
                        })
                }
            },
            submit() {
                this.loading_submit = true
                this.$http.post(`/${this.resource}`, this.form)
                    .then(response => {
                        if (response.data.success) {
                            this.$message.success(response.data.message)
                            if (this.external) {
                                this.$eventHub.$emit('reloadDataCardBrands', response.data.id)
                            } else {
                                this.$eventHub.$emit('reloadData')
                            }
                            this.close()
                        } else {
                            this.$message.error(response.data.message)
                        }
                    })
                    .catch(error => {
                        if (error.response.status === 422) {
                            this.errors = error.response.data
                        } else {
                            console.log(error)
                        }
                    })
                    .then(() => {
                        this.loading_submit = false
                    })
            },
            close() {
                this.$emit('update:showDialog', false)
                this.initForm()
            },
        }
    }
</script>
