<template>
    <el-dialog :title="titleDialog" :visible="showDialog" @close="close" @open="create" class="dialog-import">
        <form autocomplete="off" @submit.prevent="submit">
            <div class="form-body">
                <div class="row">
                    <div class="col-12 form-group" :class="{'has-danger': errors.file}">
                        <el-upload
                                ref="upload"
                                :headers="headers"
                                action="/items/import/items-update-prices"
                                :show-file-list="true"
                                :auto-upload="false"
                                :multiple="false"
                                :on-error="errorUpload"
                                :before-upload="onBeforeUpload"
                                :limit="1"
                                :data="form"
                                :on-success="successUpload">
                            <el-button slot="trigger" type="primary">Seleccione un archivo (xlsx)</el-button>
                        </el-upload>
                        <small class="form-control-feedback" v-if="errors.file" v-text="errors.file[0]"></small>
                    </div>
                    <div class="col-12 mt-4 mb-2">
                        <a class="text-dark mr-auto" href="/formats/items_update_prices.xlsx" target="_new">
                            <span class="mr-2">Descargar formato de ejemplo para importar</span>
                            <i class="fa fa-download"></i>
                        </a>
                    </div>
                </div>
            </div>
            <div class="form-actions text-right mt-5">
                <el-button class="second-buton" @click.prevent="close()">Cancelar</el-button>
                <el-button type="primary" native-type="submit" :loading="loading_submit">Procesar</el-button>
            </div>
        </form>
    </el-dialog>
</template>

<script>

    export default {
        props: ['showDialog'],
        data() {
            return {
                loading_submit: false,
                headers: headers_token,
                titleDialog: null,
                resource: 'items',
                errors: {},
                form: {},
                warehouses: []
            }
        },
        async created() {
            this.initForm();
        },
        methods: {
            onBeforeUpload(file) {

            },
            initForm() {
                this.errors = {}
                this.form = {
                    warehouse_id: null
                }
            },
            create() {
                this.titleDialog = 'Actualizar precio de los productos'
            },
            async submit() {
                this.loading_submit = true
                await this.$refs.upload.submit()
                this.loading_submit = false
            },
            close() {
                this.$emit('update:showDialog', false)
                this.initForm()
            },
            successUpload(response, file, fileList) {

                if (response.success) 
                {
                    this.$message.success(response.message)
                    this.$eventHub.$emit('reloadData')
                    this.$refs.upload.clearFiles()
                    this.close()
                } 
                else 
                {
                    this.$message({message:response.message, type: 'error'})
                }
            },
            errorUpload(error) {
                console.log(error)
            }
        }
    }
</script>
