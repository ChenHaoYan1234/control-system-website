<script setup lang="ts">
import { api_baseurl } from '~/config';


async function fetchData(deviceUuid: string): Promise<EnvDataType[] | undefined> {
    try {
        return await $fetch(`${api_baseurl}/envdata?deviceUUID=${deviceUuid}`);
    } catch (error) {
        console.error(error);
    }
}

async function fetchDeviceName(deviceUuid: string): Promise<string | undefined> {
    try {
        const res: DeviceData = await $fetch(`${api_baseurl}/device/${deviceUuid}`)
        if (res) {
            return res.deviceName
        }
    } catch (error) {
        console.error(error)
    }
}

function timestampToDate(timestamp: number) {
    const date = new Date(timestamp);
    return `${date.getFullYear()}-${date.getMonth() + 1}-${date.getDate()} ${date.getHours()}:${date.getMinutes()}:${date.getSeconds()}`;
}

const deviceUuid = useRoute().params.deviceUuid;

const deviceName = ref<string | undefined>(await fetchDeviceName(deviceUuid as string))

const envdataList = ref<EnvDataType[] | undefined>(await fetchData(deviceUuid as string))

async function refreshEnvData(){
    envdataList.value = await fetchData(deviceUuid as string);
}

</script>

<template>
    <div class="container">
        <h1 class="text-3xl font-bold mb-6 text-gray-900 dark:text-white">
            校园环境管理系统
        </h1>

        <!-- 顶部操作栏 -->
        <div class="action-bar">
            <UButton 
                icon="material-symbols:arrow-left-alt-rounded" 
                to="/"
                variant="outline"
                size="lg"
            >
                返回首页
            </UButton>
            <UButton 
                icon="material-symbols:refresh-rounded" 
                variant="outline"
                size="lg"
                :loading="!envdataList"
                @click="refreshEnvData"
            >
                刷新环境信息
            </UButton>
        </div>

        <!-- 设备信息卡片 -->
        <UCard v-if="deviceName" class="device-card">
            <template #header>
                <h3 class="text-lg font-semibold">设备信息</h3>
            </template>
            <p class="text-gray-600">设备名称：{{ deviceName }}</p>
        </UCard>

        <!-- 数据表格卡片 -->
        <UCard v-if="envdataList && envdataList.length > 0" class="data-card">
            <template #header>
                <h3 class="text-lg font-semibold">环境数据</h3>
            </template>
            <div class="table-container">
                <table class="data-table">
                    <thead>
                        <tr>
                            <th>时间</th>
                            <th>温度(°C)</th>
                            <th>湿度(%)</th>
                            <th>PM2.5(μg/m³)</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="item in envdataList" :key="item.timestamp">
                            <td>{{ timestampToDate(item.timestamp*1000) }}</td>
                            <td>{{ item.temperature }}</td>
                            <td>{{ item.humidity }}</td>
                            <td>{{ item.pm25 }}</td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </UCard>

        <!-- 空状态提示 -->
        <UCard v-else class="empty-card">
            <div class="empty-state">
                <UIcon name="material-symbols:inbox-outline" class="empty-icon" />
                <p class="text-gray-500">暂无数据</p>
            </div>
        </UCard>
    </div>
</template>

<style lang="css" scoped>
.container {
    max-width: 1200px;
    margin: 0 auto;
    padding: 2rem;
}

.action-bar {
    display: flex;
    gap: 1rem;
    margin-bottom: 2rem;
}

.device-card {
    margin-bottom: 2rem;
}

.data-card {
    overflow: hidden;
}

.table-container {
    overflow-x: auto;
}

.data-table {
    width: 100%;
    border-collapse: separate;
    border-spacing: 0;
}

.data-table th {
    background-color: #f5f5f5;
    padding: 1rem;
    text-align: left;
    font-weight: 600;
    color: #424242;
    border-bottom: 2px solid #e0e0e0;
}

.data-table td {
    padding: 1rem;
    border-bottom: 1px solid #e0e0e0;
    color: #616161;
}

.data-table tr:hover {
    background-color: #f5f5f5;
}

.data-table tr:last-child td {
    border-bottom: none;
}

.empty-card {
    margin-top: 2rem;
}

.empty-state {
    display: flex;
    flex-direction: column;
    align-items: center;
    padding: 3rem;
}

.empty-icon {
    font-size: 3rem;
    color: #9e9e9e;
    margin-bottom: 1rem;
}

/* 响应式设计 */
@media (max-width: 640px) {
    .container {
        padding: 1rem;
    }

    .action-bar {
        flex-direction: column;
    }

    .data-table {
        font-size: 0.875rem;
    }

    .data-table th,
    .data-table td {
        padding: 0.75rem 0.5rem;
    }
}
</style>