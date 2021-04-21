<template>
    <div class="com-hor-menu">
        <Menu mode="horizontal" :theme="theme1" active-name="8" @on-select="handleSelectEvent">
            <MenuItem name="1" v-show="hasMenuActions.includes('menu_operation_manage')" >
                <Icon custom="at at_yunyingshuju" />
                运营管理
            </MenuItem>
            <MenuItem name="3" v-show="hasMenuActions.includes('menu_personnel_manage')">
                <Icon custom="at at_user4" />
                人员管理
            </MenuItem>
            <MenuItem name="4" v-show="hasMenuActions.includes('menu_vehicle_manage')">
                <Icon custom="at at_car" />
                车辆管理
            </MenuItem>
            <MenuItem name="5" v-show="hasMenuActions.includes('menu_equipment_manage')">
                <Icon custom="at at_sever" />
                设备管理
            </MenuItem>
           
             <MenuItem name="7" v-show="hasMenuActions.includes('menu_realty_service')">
                <Icon custom="at at_shangjiaguanli" />
                物业服务
            </MenuItem>
            <MenuItem name="8" v-show="hasMenuActions.includes('menu_system_manage')">
                <Icon custom="at at_quick_setting" />
                系统管理
            </MenuItem>
        </Menu>
    </div>
</template>
<script>
    export default {
        name: 'HorMenu',
        data () {
            return {
                theme1: 'light'
            }
        },
        computed: {
            userType() {
                return this.$store.state.user.userType
            },
            hasMenuActions() {
                return this.$store.state.user.access
            }
        },
        methods: {
           
            handleSelectEvent(name) {
                this.$router.replace({name:'home'}).catch(err => {})
                this.$store.dispatch('hanldeTopMenuName',{ name })
            }
        }
    }
</script>

<style lang="less" scoped>

</style>
