<template>
    <div>
        <div class="page-header pr-0">
            <h2><a href="/list-reports">
                <svg  xmlns="http://www.w3.org/2000/svg" style="margin-top: -5px;" width="24"  height="24"  viewBox="0 0 24 24"  fill="none"  stroke="currentColor"  stroke-width="2"  stroke-linecap="round"  stroke-linejoin="round"  class="icon icon-tabler icons-tabler-outline icon-tabler-file-analytics"><path stroke="none" d="M0 0h24v24H0z" fill="none"/><path d="M14 3v4a1 1 0 0 0 1 1h4" /><path d="M17 21h-10a2 2 0 0 1 -2 -2v-14a2 2 0 0 1 2 -2h7l5 5v11a2 2 0 0 1 -2 2z" /><path d="M9 17l0 -5" /><path d="M12 17l0 -1" /><path d="M15 17l0 -3" /></svg>
            </a></h2>
            <ol class="breadcrumbs">
                <li class="active"><span> Consulta de detracciones </span></li>
            </ol>
        </div>
        <div class="card mb-0 pt-2 pt-md-0 tab-content-default row-new">
            <!-- <div class="card-header bg-info">
                <h3 class="my-0">Consulta de detracciones</h3>
            </div> -->
            <div class="card mb-0">
                    <div class="card-body">
                        <data-table :resource="resource">
                            <tr slot="heading">
                                <th>#</th>
                                <th >Fecha de detracción</th>
                                <th>Comprobante</th>
                                <th>Cliente</th>
                                <th class="text-center">Constancia Pago</th>
                                <th class="text-center">Total detracción</th>
                                <th class="text-center">Descarga</th>
     
                            <tr>
                            <tr slot-scope="{ index, row }">
                                <td>{{ index }}</td>  
                                <td>{{row.date_of_issue}}</td>
                                <td>{{ row.number }}<br/>
                                    <small v-text="row.document_type_description"></small><br/>
                                </td>
                                <td>{{ row.customer_name }}<br/><small v-text="row.customer_number"></small></td> 
                                <td class="text-center">{{row.detraction.pay_constancy ? row.detraction.pay_constancy:'-'}}</td> 
                                <td class="text-center">{{row.detraction.amount}}</td> 
                                <td class="text-center">
                                    <template v-if="row.image_detraction">
                                        <a :href="`${row.image_detraction}`" download class="btn waves-effect waves-light btn-xs btn-custom m-1__2">C. Pago</a>
                                    </template>
                                    <template v-else>
                                        -
                                    </template>
                                </td> 
                                
                            </tr>
                            
                        </data-table>
                         
                        
                    </div> 
            </div>
     
        </div>
    </div>
</template>

<script>
 
    import DataTable from '../../components/DataTableReports.vue'

    export default { 
        components: {DataTable},
        data() {
            return {
                resource: 'reports/document-detractions',                 
                form: {}, 

            }
        },
        async created() { 
        },
        methods: { 
             
            clickDownloadImage(image_detraction) {
                window.open(`${image_detraction}`, '_blank');
            },
        }
    }
</script>