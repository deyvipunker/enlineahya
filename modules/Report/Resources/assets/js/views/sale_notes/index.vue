<template>
    <div>
        <div class="page-header pr-0">
            <h2><a href="/list-reports">
                <svg  xmlns="http://www.w3.org/2000/svg" style="margin-top: -5px;" width="24"  height="24"  viewBox="0 0 24 24"  fill="none"  stroke="currentColor"  stroke-width="2"  stroke-linecap="round"  stroke-linejoin="round"  class="icon icon-tabler icons-tabler-outline icon-tabler-file-analytics"><path stroke="none" d="M0 0h24v24H0z" fill="none"/><path d="M14 3v4a1 1 0 0 0 1 1h4" /><path d="M17 21h-10a2 2 0 0 1 -2 -2v-14a2 2 0 0 1 2 -2h7l5 5v11a2 2 0 0 1 -2 2z" /><path d="M9 17l0 -5" /><path d="M12 17l0 -1" /><path d="M15 17l0 -3" /></svg>
            </a></h2>
            <ol class="breadcrumbs">
                <li class="active"><span> Consulta de Notas de venta </span></li>
            </ol>
        </div>
        <div class="card mb-0 pt-2 pt-md-0 tab-content-default row-new">
            <div class="p-b-30">
                <!-- <h3 class="my-0">Consulta de Notas de venta</h3> -->
    
                <div class="data-table-visible-columns">
                    <el-dropdown :hide-on-click="false">
                        <el-button type="primary">
                            Mostrar/Ocultar columnas<i class="el-icon-arrow-down el-icon--right"></i>
                        </el-button>
                        <el-dropdown-menu slot="dropdown">
                            <el-dropdown-item v-for="(column, index) in columns" :key="index">
                                <el-checkbox  @change="generalSetColumnsToShow(true)" v-model="column.visible">{{ column.title }}</el-checkbox>
                            </el-dropdown-item>
                        </el-dropdown-menu>
                    </el-dropdown>
                </div>
            </div>
    
    
    
            <div class="card mb-0">
                    <div class="card-body">
                        <data-table :resource="resource" :applyCustomer="true">
                            <tr slot="heading">
                                <th>#</th>
                                <th class="text-center">Fecha Emisión</th>
                                <th class="text-center">Hora Emisión</th>
                                <th class="">Usuario/Vendedor</th>
                                <th>Cliente</th>
                                <th>Nota de Venta</th>
                                <th class="text-center">Estado pago</th>
                                <th>Estado</th>
                                <th class="text-center">Moneda</th>
                                <th class="text-center" v-if="columns.web_platforms.visible">Plataforma</th>
                                <th>Orden de compra</th>
                                <th class="text-center" v-if="columns.region.visible">Region</th>
                                <th class="text-center">Comprobantes</th>
                                <th>Cotización</th>
                                <th>Caso</th>
    
                                <th class="text-center">Productos</th>
                                <th class="text-right">Descuento</th>
    
                                <th class="text-right" >T.Exportación</th>
                                <th class="text-right" >T.Inafecta</th>
                                <th class="text-right" >T.Exonerado</th>
    
                                <th class="text-right">T.Gravado</th>
                                <th class="text-right">T.Igv</th>
                                <th class="text-right">Total</th>
                                
                                <template v-if="configuration.enabled_sales_agents">
                                    <th>Agente</th>
                                    <th>Datos de referencia</th>
                                </template>
    
                            <tr>
                            <tr slot-scope="{ index, row }">
                                <td>{{ index }}</td>
                                <td>{{row.date_of_issue}}</td>
                                <td>{{row.time_of_issue}}</td>
                                <td>{{row.user_name}}</td>
                                <td>{{row.customer_name}}</td>
                                <td>{{row.number_full}}</td>
                                <td class="text-center">
                                    <span class="badge text-white" :class="{'bg-success': (row.total_canceled), 'bg-warning': (!row.total_canceled)}">{{row.total_canceled ? 'Pagado':'Pendiente'}}</span>
                                </td>
                                <td>{{row.state_type_description}}</td>
                                <td>{{row.currency_type_id}}</td>
                            <td  v-if="columns.web_platforms.visible">
                                <template v-for="(platform,i) in row.web_platforms" v-if="row.web_platforms !== undefined">
                                    <label class="d-block"  :key="i">{{platform.name}}</label>
                                </template>
                            </td>
                            <td>{{ row.purchase_order }}</td>
                            <td v-if="columns.region.visible">{{row.customer_region}}</td>
                            <td>
                                    <template v-for="(doc,i) in row.documents">
                                        <label class="d-block"  :key="i">{{doc.number_full}}</label>
                                    </template>
                                </td>
                            <td>{{row.quotation_number_full}}</td>
                                <td>{{row.sale_opportunity_number_full}}</td>
    
    
                                <td class="text-center">
                                    <el-popover
                                        placement="right"
                                        width="400"
                                        trigger="click">
                                        <el-table :data="row.items_for_report">
                                            <el-table-column width="80" property="index" label="#"></el-table-column>
                                            <el-table-column width="220" property="description" label="Producto"></el-table-column>
                                            <el-table-column width="90" property="quantity" label="Cantidad"></el-table-column>
                                        </el-table>
                                        <el-button slot="reference"> <i class="fa fa-eye"></i></el-button>
                                    </el-popover>
                                </td>
    
                                <td >{{ (row.state_type_id == '11') ? "0.00" : row.total_discount }}</td>
    
    
                                <td >{{ (row.state_type_id == '11') ? "0.00" : row.total_exportation }}</td>
                                <td >{{ (row.state_type_id == '11') ? "0.00" : row.total_unaffected }}</td>
                                <td >{{ (row.state_type_id == '11') ? "0.00" : row.total_exonerated }}</td>
    
                                <td>{{ (row.state_type_id == '11') ? "0.00" : row.total_taxed}}</td>
                                <td>{{ (row.state_type_id == '11') ? "0.00" : row.total_igv}}</td>
                                <td>{{ (row.state_type_id == '11') ? "0.00" : row.total}}</td>
    
                                <template v-if="configuration.enabled_sales_agents">
                                    <td>{{ row.agent_name }}</td>
                                    <td>{{ row.reference_data }}</td>
                                </template>
                            </tr>
    
                        </data-table>
    
    
                    </div>
            </div>
    
        </div>
    </div>
</template>

<script>

    import DataTable from '../../components/DataTableReports.vue'
    import {fnListVisibleColumns} from '@mixins/functions'

    export default {
        props: ['configuration'],
        components: {DataTable},
        mixins: [fnListVisibleColumns],
        data() {
            return {
                resource: 'reports/sale-notes',
                form: {},
                columns: {
                    web_platforms: {
                        title: 'Plataformas web',
                        visible: false
                    },
                    region: {
                        title: 'Region',
                        visible: false
                    },
                },
                name_report_colums: 'sale_notes_reports_index'

            }
        },
        async created() 
        {
            this.generalSetColumnsToShow()
        },
        methods: {

        }
    }
</script>
