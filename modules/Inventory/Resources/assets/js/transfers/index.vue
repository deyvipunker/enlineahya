<template>
    <div>
        <div class="page-header pr-0">
            <h2>
                <a href="/transfers">
                    <svg  xmlns="http://www.w3.org/2000/svg" style="margin-top: -5px;" width="24"  height="24"  viewBox="0 0 24 24"  fill="none"  stroke="currentColor"  stroke-width="2"  stroke-linecap="round"  stroke-linejoin="round"  class="icon icon-tabler icons-tabler-outline icon-tabler-building-warehouse"><path stroke="none" d="M0 0h24v24H0z" fill="none"/><path d="M3 21v-13l9 -4l9 4v13" /><path d="M13 13h4v8h-10v-6h6" /><path d="M13 21v-9a1 1 0 0 0 -1 -1h-2a1 1 0 0 0 -1 1v3" /></svg>
                </a>
            </h2>
            <ol class="breadcrumbs">
                <li class="active">
                    <span>{{ title }}</span>
                </li>
            </ol>
            <div class="right-wrapper pull-right">
                <a class="btn btn-custom btn-sm mt-2 mr-2"
                   href="#"
                   @click.prevent="clickCreate()">
                    <i class="fa fa-plus-circle"></i> Nuevo
                </a>
            </div>
        </div>
        <div class="card tab-content-default row-new mb-0">
            <!-- <div class="card-header bg-info">
                <h3 class="my-0">Listado de {{ title }}</h3>
            </div> -->
            <div class="card-body">
                <data-table :resource="resource">
                    <tr slot="heading">
                        <th>#</th>
                        <th>Fecha</th>
                        <th>Almacen Inicial</th>

                        <th>Almacen Destino</th>
                        <th>Detalle</th>
                        <th>Detalle Productos</th>
                        <th>Cantidad Total Productos</th>
                        <th class="text-right">Acciones</th>
                    </tr>
                    <tr></tr>
                    <tr slot-scope="{ index, row }">
                        <td>{{ index }}</td>
                        <td>{{ row.created_at }}</td>

                        <td>{{ row.warehouse }}</td>
                        <td>{{ row.warehouse_destination }}</td>
                        <td>{{ row.description }}</td>
                        <td>
                            <el-popover placement="right"
                                        trigger="click"
                                        width="500">
                                <el-table :data="row.inventory">
                                    <el-table-column label="Producto"
                                                     property="description"
                                                     width="260"></el-table-column>

                                    <el-table-column label="Cantidad"
                                                     property="quantity"></el-table-column>

                                    <el-table-column label="Series/Lotes">
                                        <template slot-scope="scope"
                                                  width="100">
                                            <ul v-if="scope.row.lots" class="list-unstyled">
                                                <li v-for="(item, index) in scope.row.lots" :key="index">
                                                    {{ item.series }}
                                                </li>
                                            </ul>
                                            <ul v-if="scope.row.lots_enabled" class="list-unstyled">
                                                <li v-for="(item, index) in scope.row.lot_codes" :key="index">
                                                    {{ item.lot_code }}
                                                </li>
                                            </ul>
                                        </template>
                                    </el-table-column>
                                </el-table>
                                <el-button slot="reference"
                                           icon="el-icon-zoom-in"></el-button>
                            </el-popover>
                        </td>
                        <td>{{ row.quantity }}</td>
                        <td class="text-right">
                            <button
                                class="btn waves-effect waves-light btn-xs btn-info"
                                type="button"
                                @click.prevent="clickDownload('pdf',row.id)"
                            >
                                <i class="fa fa-file-pdf"></i>
                                PDF
                            </button>
                        </td>
                        <!--<td class="text-right">
                                         <button type="button" class="btn waves-effect waves-light btn-xs btn-info"
                                                @click.prevent="clickCreate(row.id)">Editar</button>
                                        <button type="button" class="btn waves-effect waves-light btn-xs btn-danger"
                                                @click.prevent="clickDelete(row.id)">Eliminar</button>
                        </td>-->
                    </tr>
                </data-table>
            </div>

            <inventories-form :recordId="recordId"
                              :showDialog.sync="showDialog"></inventories-form>
        </div>
    </div>
</template>

<script>
import DataTable from "../../../../../../resources/js/components/DataTableTransfers.vue";
import {deletable} from "../../../../../../resources/js/mixins/deletable";
import InventoriesForm from "./form.vue";

export default {
    components: {DataTable, InventoriesForm},
    mixins: [deletable],
    data() {
        return {
            title: null,
            showDialog: false,
            resource: "transfers",
            recordId: null,
            typeTransaction: null
        };
    },
    created() {
        this.title = "Traslados";
    },
    methods: {
        clickCreate(recordId = null) {
            location.href = `/${this.resource}/create`;
            //this.recordId = recordId
            //this.showDialog = true
        },
        clickDelete(id) {
            this.destroy(`/${this.resource}/${id}`).then(() =>
                this.$eventHub.$emit("reloadData")
            );
        },
        clickDownload(type,id) {
            window.open(`/${this.resource}/download/${type}/${id}`, "_blank");
        },
    }
};
</script>
