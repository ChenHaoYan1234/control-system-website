<script setup lang="ts">
import { api_baseurl } from '~/config'



const props = defineProps({
    deviceName: {
        type: String,
        required: true
    },
    deviceUuid: {
        type: String,
        required: true
    }
})

let device = props.deviceName
let old_device = props.deviceName

const envdata_link = `/envdata/${props.deviceUuid}`

const changingDeviceName = ref<boolean>(false)

function startEdit() {
    changingDeviceName.value = true
    old_device = device
}

async function saveDeviceName() {
    try{
    changingDeviceName.value = false
    await $fetch(`${api_baseurl}/device`, {
        method: "PUT",
        body: {
            deviceName: device,
            deviceUUID: props.deviceUuid
        }
    })
    }catch (e){
        console.log(e)
        device = old_device
        changingDeviceName.value = false
    }
}

function unSaveDeviceName() {
    device = old_device
    changingDeviceName.value = false
}

</script>

<template>
    <div>
        <div>
            设备名称:
            <span v-if="!changingDeviceName" class="inline">{{ device }}</span>
            <UInput v-if="changingDeviceName" v-model="device" class="inline" />
            <UButton v-if="!changingDeviceName" icon="material-symbols:edit-square-outline" @click="startEdit" />
            <UButton v-if="changingDeviceName" icon="material-symbols:check-rounded" @click="saveDeviceName" />
            <UButton v-if="changingDeviceName" icon="material-symbols:close-rounded" @click="unSaveDeviceName" />
            <p style="display: inline-block;margin-left: 10px;">UUID: {{ deviceUuid }}</p>
            <UButton style="display: inline-block;margin-left: 10px;" :to="envdata_link" icon="material-symbols:subdirectory-arrow-left-rounded"/>
        </div>
        
    </div>
</template>

<style lang="css" scoped>
.inline {
    display: inline-block;
    margin-right: 10px;
}
</style>