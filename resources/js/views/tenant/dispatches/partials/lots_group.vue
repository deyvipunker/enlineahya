<template>
    <el-dialog
        :title="titleDialog"
        width="50%"
        :visible="showDialog"
        @open="create"
        :close-on-click-modal="false"
        :close-on-press-escape="false"
        append-to-body
        :show-close="false"
    >
        <div class="form-body">
            <div class="row">
                <div class="col-md-12" style="word-break: break-word;">
                    <span>Si al seleccionar lotes la cantidad es mayor, el último lote quedara con la diferencia. </span>
                </div>
            </div>
            <div class="row">
                <div class="col-lg-5 col-md-5 col-sm-12 pb-2">
                    <el-input placeholder="Buscar código ..."
                              v-model="search"
                              style="width: 100%;"
                              prefix-icon="el-icon-search"
                              @input="filter">
                    </el-input>
                </div>

                <div class="col-lg-5 col-md-5 col-sm-12 pb-2" v-if="showCompromiseAllQuantity">
                    <el-checkbox v-model="set_compromise_all_quantity" @change="changeCompromiseAllQuantity">Comprometer
                        todos los lotes
                    </el-checkbox>
                </div>

                <div class="col-lg-12 col-md-12">
                    <table class="table">
                        <thead>
                        <tr>
                            <th width="145">Comprometer</th>
                            <th></th>
                            <th>Código</th>
                            <th>Cantidad</th>
                            <th>Fecha vencimiento
                                <el-button icon="el-icon-d-caret" @click="orderData()" plain></el-button>
                            </th>
                        </tr>
                        </thead>
                        <tbody>
                        <tr
                            v-for="(row, index) in lotsGroupOrdered"
                            :key="index"
                            v-show="row.quantity > 0"
                        >
                            <th>
                                <el-input-number v-model="row.compromise_quantity"
                                                 v-bind:class="{ 'text-danger': (row.compromise_quantity > row.quantity) }"></el-input-number>
                            </th>

                            <th>
                                <el-button icon="el-icon-d-arrow-left" round @click="setTotalQuantityToCompromise(row)">
                                    <el-tooltip class="item"
                                        content="Se agregan todos"
                                        effect="dark" placement="top-start">
                                        <i class="fa fa-info-circle"></i>
                                    </el-tooltip>
                                </el-button>
                            </th>

                            <th>{{ row.code }}</th>
                            <th class>{{ row.quantity }}</th>
                            <th class>{{ row.date_of_due }}</th>
                        </tr>
                        </tbody>
                    </table>
                </div>
            </div>
        </div>

        <div class="form-actions text-right pt-2">
            <el-button @click.prevent="close()">Cerrar</el-button>
            <el-button type="primary" @click="submit">Guardar</el-button>
        </div>
    </el-dialog>
</template>

<script>
export default {
    name: 'ItemLotsGroup',
    props: ["showDialog",
        'lotsGroupAll',
        'lotsGroup',
        'stock',
        'recordId',
        'quantity',
        'compromiseAllQuantity'
    ],
    data() {
        return {
            titleDialog: "Lotes",
            loading: false,
            errors: {},
            form: {},
            idSelected: null,
            search: '',
            lots_group_: [],
            orderT: 'desc',
            set_compromise_all_quantity: false,
        };
    },
    async created() {

    },
    computed: {
        lotsGroupOrdered() {
            return _.orderBy(this.lots_group_, 'date_of_due', this.orderT)
        },
        quantityCompleted() {
            return this.lots_group_.filter(x => x.checked == true).reduce((accum, item) => accum + Number(item.quantity), 0) >= this.quantity
        },
        toAttend() {
            return this.quantity - this.lots_group_.filter(x => x.compromise_quantity > 0).reduce((accum, item) => accum + Number(item.compromise_quantity), 0)
        },
        showCompromiseAllQuantity() {
            if (this.compromiseAllQuantity !== undefined && this.compromiseAllQuantity) return this.compromiseAllQuantity

            return false
        }
    },
    methods: {
        changeCompromiseAllQuantity() {
            this.lotsGroupOrdered.forEach(row => {

                if (this.set_compromise_all_quantity) {
                    this.setTotalQuantityToCompromise(row, false)
                } else {
                    row.compromise_quantity = 0
                }
            })
            this.$message.success('La cantidad comprometida de todos los lotes fue actualizada')
        },
        setTotalQuantityToCompromise(row, show_message = true) {
            row.compromise_quantity = parseFloat(row.quantity)
            if (show_message) this.$message.success('Cantidad asignada')
        },
        orderData() {
            this.orderT = this.orderT == 'desc' ? 'asc' : 'desc'
        },
        filter() {
            if(_.isNull(this.lotsGroupAll) ||_.isUndefined(this.lotsGroupAll)) {
                if (this.search) {
                    this.lots_group_ = _.filter(this.lotsGroup, x => x.code.toUpperCase().includes(this.search.toUpperCase()))
                } else {
                    this.lots_group_ = this.lotsGroup
                }
            } else {
                if (this.search) {
                    this.lots_group_ = _.filter(this.lotsGroupAll, x => x.code.toUpperCase().includes(this.search.toUpperCase()))
                } else {
                    this.lots_group_ = this.lotsGroupAll
                }
            }
       },
        changeSelect(event, index) {
            /*if(this.quantityCompleted) {
                this.$message.warning('La cantidad está completada');
            }*/
            this.lots_group_[index].checked = event
            this.idSelected = this.lots_group_.filter(x => x.checked == true).map(x => x.id);
            let sum = this.lots_group_.filter(x => x.checked == true).reduce((accum, item) => accum + Number(item.quantity), 0)
            if (sum >= this.quantity) {
                this.$message.warning('La cantidad está completada.');
            }
        },
        handleSelectionChange(val) {
            //this.$refs.multipleTable.clearSelection();
            let row = val[val.length - 1];
            this.multipleSelection = [row];
        },
        create() {
            this.filter()
            this.set_compromise_all_quantity = false
        },
        async submit() {

            let compromise_quantity = this.lots_group_.filter(x => x.compromise_quantity > 0).reduce((accum, item) => accum + Number(item.compromise_quantity), 0)
            let quantity = compromise_quantity

            const successValid = this.lots_group_.filter(x => x.compromise_quantity > 0).filter(x => x.compromise_quantity > x.quantity)
            if (successValid.length) {
                return this.$message.warning('La cantidades comprometida de un lote no debe sobrepasar su capacidad.');
            }

            const lots_selecteds = this.lots_group_.filter(x => x.compromise_quantity > 0).map(item => {
                return {
                    id: item.id,
                    code: item.code,
                    compromise_quantity: item.compromise_quantity,
                    date_of_due: item.date_of_due,
                }
            })
            await this.$emit("addRowLotGroup", lots_selecteds,quantity);
            await this.$emit("update:showDialog", false);
        },

        close() {
            this.$emit("update:showDialog", false);
        },
    },
};
</script>
