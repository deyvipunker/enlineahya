<template>
    <div id="styleSwitcher" class="style-switcher" style="z-index: 1040;">

        <!-- <a id="styleSwitcherOpen" class="style-switcher-open" href="#">
            <i class="fas fa-paint-brush"></i>
        </a> -->

        <form class="style-switcher-wrap" autocomplete="off">
            <h3>Estilos y Temas <a class="style-switcher-open close-config" href="#">+</a></h3>
            
            <div v-if="visual == null">
                <h5 class="">No posee ajustes actualmente</h5>
                <a href="" class="text-warning" v-if="typeUser != 'integrator'">cargar ajustes por defecto</a>
                <br>
            </div>
            <div v-if="typeUser != 'integrator'" class="pt-3">
                <div style="background-color: #283046;">
                    <a v-if="visuals.bg == 'white'"
                        href="/configurations/change-mode"
                        class="notification-icon btn btn-dark btn-sm btn-block"
                        data-toggle="tooltip"
                        data-placement="bottom"
                        title="Modo noche"
                        style="text-decoration: none;">
                        <i class="fas fa-moon"></i> Modo oscuro
                    </a>
                    <a v-if="visuals.bg == 'dark'"
                        href="/configurations/change-mode"
                        class="notification-icon btn btn-light btn-sm btn-block btn-light-mode"
                        data-toggle="tooltip"
                        data-placement="bottom"
                        title="Modo día"
                        style="text-decoration: none;">
                        <i class="fas fa-sun"></i> Modo Claro
                    </a>
                </div>
                <div class="pt-3 d-none">
                    <h5>Color de fondo del sidebar</h5>
                    <div class="form-group el-custom-control">
                        <button :class="{ 'active': visuals.sidebar_theme === 'white' }" type="button" @click="onChangeBgSidebar('white')" class="btn flex-fill" style="background-color: #ffffff;"></button>
                        <button :class="{ 'active': visuals.sidebar_theme === 'blue' }" type="button" @click="onChangeBgSidebar('blue')" class="btn flex-fill" style="background-color: #7367f0;"></button>
                        <!-- <button :class="{ 'active': visuals.sidebar_theme === 'gray' }" type="button" @click="onChangeBgSidebar('gray')" class="btn" style="background-color: #82868b;"></button> -->
                        <button :class="{ 'active': visuals.sidebar_theme === 'green' }" type="button" @click="onChangeBgSidebar('green')" class="btn flex-fill" style="background-color: #28c76f;"></button>
                        <button :class="{ 'active': visuals.sidebar_theme === 'red' }" type="button" @click="onChangeBgSidebar('red')" class="btn flex-fill" style="background-color: #ea5455;"></button>
                        <!-- <button :class="{ 'active': visuals.sidebar_theme === 'warning' }" type="button" @click="onChangeBgSidebar('warning')" class="btn" style="background-color: #ff9f43;"></button> -->
                        <!-- <button :class="{ 'active': visuals.sidebar_theme === 'ligth-blue' }" type="button" @click="onChangeBgSidebar('ligth-blue')" class="btn" style="background-color: #00cfe8;"></button> -->
                        <button :class="{ 'active': visuals.sidebar_theme === 'dark' }" type="button" @click="onChangeBgSidebar('dark')" class="btn flex-fill" style="background-color: #283046;"></button>
                    </div>
                </div>

                <div class="mt-3">
                    <h5>Selecciona un color de tema:</h5>
                    <div class="color-selector">
                      <button v-for="(colors, theme) in themes" :key="theme" 
                              :style="{ backgroundColor: colors['--color-visual'] }" 
                              @click="applyTheme(theme)">
                      </button>
                    </div>
                </div>

                <div class="pt-3">
                    <h5>Menú lateral contraído</h5>
                    <div :class="{'has-danger': errors.compact_sidebar}">
                        <el-switch
                            v-model="form.compact_sidebar"
                            active-text="Si"
                            inactive-text="No"
                            @change="submitForm">
                        </el-switch>
                        <br>
                        <small class="form-control-feedback" v-if="errors.compact_sidebar" v-text="errors.compact_sidebar[0]"></small>
                    </div>
                </div>

                <div class="mt-3">
                    <h5>Mostrar panel de bienvenida en el dashboard</h5>
                    <div>
                        <el-switch
                            v-model="showWelcome"
                            active-text="Si"
                            inactive-text="No"
                            @change="updateConfig">
                        </el-switch>
                    </div>
                </div>

                <div class="pt-3 form-modern">
                    <label class="control-label">Cantidad de columnas en POS</label>
                    <div :class="{'has-danger': errors.amount_plastic_bag_taxes}">
                        <el-select v-model="form.colums_grid_item" @change="submitForm">
                            <el-option label="3" value="3"></el-option>
                            <el-option label="4" value="4"></el-option>
                            <el-option label="5" value="5"></el-option>
                            <el-option label="6" value="6"></el-option>
                        </el-select>
                        <small class="form-control-feedback" v-if="errors.amount_plastic_bag_taxes" v-text="errors.amount_plastic_bag_taxes[0]"></small>
                    </div>
                </div>

                <div class="pt-3 form-modern">
                    <label class="control-label">Cambiar tema</label>
                    <div :class="{'has-danger': errors.compact_sidebar}">
                        <el-select
                            v-model="form.skin_id"
                            placeholder="Tema"
                            @change="submitForm"
                            class="pb-3">
                            <el-option
                                v-for="item in skins"
                                :key="item.id"
                                :label="item.name"
                                :value="item.id">
                            </el-option>
                        </el-select>
                        <small class="form-control-feedback" v-if="errors.compact_sidebar" v-text="errors.compact_sidebar[0]"></small>
                        <el-button class="second-buton" type="button" @click="dialogSkins()" color="primary">Subir tema</el-button>
                    </div>
                </div>

            </div>
        </form>
        <dialog-skins :showDialog.sync="dialogSkinsVisible" :skins.sync="skins"/>
    </div>
</template>

<script>
    import DialogSkins from './partials/dialog_skins.vue'
    export default {
        props:['visual','typeUser'],
        components: {
            DialogSkins
        },
        data() {
            return {
                themes: {
                    white: {
                        "--color-visual": "#0c7286",
                        "--primary-color": "#0c7286",
                        "--dark-color": "#001524",
                        "--light-color": "#eef5f5",
                        "--light2-color": "#d5e8e8",
                        "--light3-color": "#75e4e4"
                    },
                    blue: {
                        "--color-visual": "#7367f0",
                        "--primary-color": "#7367f0",
                        "--dark-color": "#1a1b4b",
                        "--light-color": "#e3e3ff",
                        "--light2-color": "#c0c0ff",
                        "--light3-color": "#9090ff"
                    },
                    green: {
                        "--color-visual": "#28c76f",
                        "--primary-color": "#28c76f",
                        "--dark-color": "#0a3d27",
                        "--light-color": "#d9f5e3",
                        "--light2-color": "#a8e6cb",
                        "--light3-color": "#75c2a2"
                    },
                    red: {
                        "--color-visual": "#ea5455",
                        "--primary-color": "#ea5455",
                        "--dark-color": "#520000",
                        "--light-color": "#fddddd",
                        "--light2-color": "#f8a6a6",
                        "--light3-color": "#f37171"
                    },
                    retro: {
                        "--color-visual": "#ece3ca",
                        "--primary-color": "#2e282a",
                        "--dark-color": "#282425",
                        "--light-color": "#ece3ca",
                        "--light2-color": "#e4d8b4",
                        "--light3-color": "#e0c881"
                    }
                },
                showWelcome: localStorage.getItem('show_welcome_panel') === 'true', 
                loading_submit: false,
                resource: 'configurations',
                errors: {},
                form: {},
                visuals: {},
                skins: {},
                dialogSkinsVisible: false
            }
        },
        async created() {
            await this.initForm()
            await this.getRecords()

            await this.initForm();
            const savedTheme = localStorage.getItem('selectedTheme');
            if (savedTheme && this.themes[savedTheme]) {
                this.applyTheme(savedTheme);
            }
        },
        methods: {
            updateConfig() {
                localStorage.setItem('show_welcome_panel', this.showWelcome);
                this.toggleWelcomeComponent();
            },
            toggleWelcomeComponent() {
                const welcomeComponent = document.querySelector('.welcome-component');
                if (this.showWelcome) {
                    welcomeComponent.style.display = 'block';
                } else {
                    welcomeComponent.style.display = 'none';
                }
            },
            applyTheme(theme) {
                const colors = this.themes[theme];
                Object.keys(colors).forEach(variable => {
                    document.documentElement.style.setProperty(variable, colors[variable]);
                });

                if (theme === "retro") {
                    document.documentElement.style.setProperty("--font-family", "'PT Mono', sans-serif");
                } else {
                    document.documentElement.style.setProperty("--font-family", "initial");
                }

                localStorage.setItem('selectedTheme', theme);
            },
            onChangeBgSidebar(theme) {
                this.visuals.sidebar_theme = theme;
                this.submit();
            },
            initForm() {
                this.errors = {}
                this.form = {
                    id: 1,
                    compact_sidebar: true,
                    colums_grid_item: 4,
                    enable_whatsapp: true,
                    phone_whatsapp: '',
                    skins: 1,
                }
            },
            getRecords() {
                this.$http.get(`/${this.resource}/record`) .then(response => {
                    if (response.data !== ''){
                        this.visuals = response.data.data.visual;
                        this.form = response.data.data;
                        this.skins = response.data.data.skins;
                    }
                });
            },
            submit() {
                this.visuals.navbar = 'fixed';
                this.$http.post(`/${this.resource}/visual_settings`, this.visuals).then(response => {
                    if (response.data.success) {
                        this.$message.success(response.data.message);
                    }
                    else {
                        this.$message.error(response.data.message);
                    }
                }).catch(error => {
                    if (error.response.status === 422) {
                        this.errors = error.response.data.errors;
                    }
                    else {
                        console.log(error);
                    }
                }).then(() => {
                    location.reload();
                });
            },
            submitForm() {
                this.loading_submit = true;
                this.$http.post(`/${this.resource}`, this.form).then(response => {
                    if (response.data.success) {
                        this.$message.success(response.data.message);
                        location.reload()
                    }
                    else {
                        this.$message.error(response.data.message);
                    }
                }).catch(error => {
                    if (error.response.status === 422) {
                        this.errors = error.response.data.errors;
                    }
                    else {
                        console.log(error);
                    }
                }).then(() => {
                    this.loading_submit = false;
                });
            },
            dialogSkins() {
                this.dialogSkinsVisible = true
            },
        },
        mounted() {
            this.toggleWelcomeComponent();
        }
    }
</script>
<style scoped lang=scss>
.el-custom-control{
    display: flex;
    align-content: center;
    .btn{
        margin-right: .5rem;
        $size: 20px;
        width: $size;
        height: $size;
        border-radius: 4px;
        padding: 0;
        &.active{
            box-shadow: 0 0 0 0.2rem rgba(38, 143, 255, .5);
        }
    }
}
.color-selector {
    display: flex;
    gap: 10px;
}
.color-selector button {
    width: 40px;
    height: 20px;
    border: none;
    border-radius: 10%;
    cursor: pointer;
    outline: none;
}
</style>