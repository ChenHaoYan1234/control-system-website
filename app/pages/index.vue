<script setup lang="ts">
import { api_baseurl } from '~/config'


async function getDeviceList(): Promise<DeviceData[] | undefined> {
    try {
        return await $fetch(`${api_baseurl}/device`)
    } catch (e) {
        console.error(e)
    }
}

const deviceList = ref<DeviceData[] | undefined>(await getDeviceList())

async function refreshDeviceList() {
    deviceList.value = await getDeviceList()
}

</script>

<template>
    <div>
        <UButton icon="material-symbols:refresh-rounded" @click="refreshDeviceList">
            刷新设备
        </UButton>
        <div v-if="deviceList">
            <div v-for="device in deviceList" :key="device.deviceUUID">
                <DeviceView :device-uuid="device.deviceUUID" :device-name="device.deviceName" />
            </div>
        </div>
        <div v-else> 无法连接到服务器 </div>
    </div>
</template>