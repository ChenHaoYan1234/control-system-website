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
    <div>
        <UButton icon="material-symbols:arrow-left-alt-rounded" to="/">返回首页</UButton>
        <UButton icon="material-symbols:refresh-rounded" style="margin-left: 20px;" @click="refreshEnvData">
            刷新环境信息
        </UButton>
        <p v-if="deviceName">设备名称：{{ deviceName }}</p>
        <div v-if="envdataList && envdataList.length > 0">
            <table>
                <thead>
                    <tr>
                        <th>时间</th>
                        <th>温度(°C)</th>
                        <th>湿度(%)</th>
                        <th>PM2.5(μg/m3)</th>
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
        <div v-else>
            <p>暂无数据</p>
        </div>
    </div>
</template>

<style lang="css" scoped>
td{
    padding: 10px;
    border: 1px solid #ccc;
    text-align: center;
}
</style>