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
const isLoading = ref(false)

async function refreshDeviceList() {
    isLoading.value = true
    deviceList.value = await getDeviceList()
    isLoading.value = false
}

</script>

<template>
    <div class="container mx-auto px-4 py-8">
        <h1 class="text-3xl font-bold mb-6 text-gray-900 dark:text-white">
            校园环境管理系统
        </h1>

        <!-- 顶部操作栏 -->
        <UCard class="mb-6">
            <div class="flex items-center justify-between">
                <h2 class="text-xl font-semibold text-gray-900 dark:text-white">
                    设备列表
                </h2>
                <UButton 
                    icon="material-symbols:refresh-rounded" :loading="isLoading" color="primary" variant="solid"
                    @click="refreshDeviceList">
                    刷新设备
                </UButton>
            </div>
        </UCard>

        <!-- 设备列表 -->
        <div v-if="deviceList" class="grid gap-4 md:grid-cols-2 lg:grid-cols-3">
            <UCard v-for="device in deviceList" :key="device.deviceUUID" class="hover:shadow-lg transition-shadow">
                <DeviceView :device-uuid="device.deviceUUID" :device-name="device.deviceName" />
            </UCard>
        </div>

        <!-- 加载状态 -->
        <UCard v-else-if="isLoading" class="text-center py-8">
            <UIcon name="material-symbols:hourglass-empty" class="text-4xl mb-4 animate-spin" />
            <span class="text-gray-600 dark:text-gray-400">加载中...</span>
        </UCard>

        <!-- 错误状态 -->
        <UCard v-else class="text-center py-8">
            <UIcon name="material-symbols:error-outline" class="text-4xl mb-4 text-red-500" />
            <span class="text-gray-600 dark:text-gray-400">无法连接到服务器</span>
        </UCard>
    </div>
</template>

<style scoped>
.container {
    max-width: 1200px;
    margin: 0 auto;
    padding: 2rem;
}
</style>