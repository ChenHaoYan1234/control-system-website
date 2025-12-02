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
const loading = ref(false)

function startEdit() {
    changingDeviceName.value = true
    old_device = device
}

async function saveDeviceName() {
    loading.value = true
    try {
        changingDeviceName.value = false
        await $fetch(`${api_baseurl}/device`, {
            method: "PUT",
            body: {
                deviceName: device,
                deviceUUID: props.deviceUuid
            }
        })
    } catch (e) {
        console.log(e)
        device = old_device
    } finally {
        loading.value = false
    }
}

function unSaveDeviceName() {
    device = old_device
    changingDeviceName.value = false
}

</script>

<template>
    <UCard class="device-card">
        <div class="device-content">
            <div class="device-info">
                <div class="device-name-section">
                    <span class="label">设备名称:</span>
                    <span v-if="!changingDeviceName" class="device-name">{{ device }}</span>
                    <UInput 
                        v-else
                        v-model="device" 
                        class="device-name-input"
                        :disabled="loading"
                    />
                </div>
                <div class="device-uuid">
                    <span class="label">UUID:</span>
                    <span class="uuid-text">{{ deviceUuid }}</span>
                </div>
            </div>
            <div class="device-actions">
                <template v-if="!changingDeviceName">
                    <UButton
                        icon="material-symbols:edit-square-outline"
                        variant="outline"
                        size="sm"
                        class="edit-button"
                        @click="startEdit"
                    />
                </template>
                <template v-else>
                    <UButton
                        icon="material-symbols:check-rounded"
                        size="sm"
                        class="save-button"
                        :loading="loading"
                        @click="saveDeviceName"
                    />
                    <UButton
                        icon="material-symbols:close-rounded"
                        variant="outline"
                        size="sm"
                        class="cancel-button"
                        :disabled="loading"
                        @click="unSaveDeviceName"
                    />
                </template>
                <UButton
                    :to="envdata_link"
                    icon="material-symbols:subdirectory-arrow-left-rounded"
                    variant="ghost"
                    size="sm"
                    class="nav-button"
                />
            </div>
        </div>
    </UCard>
</template>

<style lang="css" scoped>
.device-card {
    margin: 0.5rem 0;
    transition: transform 0.2s ease;
    background: #ffffff;
}

.device-card:hover {
    transform: translateY(-2px);
    box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -1px rgba(0, 0, 0, 0.06);
}

.device-content {
    display: flex;
    justify-content: space-between;
    align-items: center;
    gap: 1rem;
}

.device-info {
    display: flex;
    flex-direction: column;
    gap: 0.5rem;
}

.device-name-section {
    display: flex;
    align-items: center;
    gap: 0.5rem;
}

.device-name {
    font-weight: 500;
    color: #1a1a1a;
}

.device-name-input {
    max-width: 200px;
}

.device-uuid {
    display: flex;
    align-items: center;
    gap: 0.5rem;
    font-size: 0.875rem;
    color: #666666;
}

.uuid-text {
    font-family: monospace;
}

.device-actions {
    display: flex;
    gap: 0.5rem;
}

.label {
    color: #666666;
    font-weight: 500;
}

.edit-button {
    --at-apply: "text-gray-700 border-gray-300 hover:bg-gray-50";
}

.save-button {
    --at-apply: "bg-green-600 hover:bg-green-700 text-white";
}

.cancel-button {
    --at-apply: "text-red-600 border-red-300 hover:bg-red-50";
}

.nav-button {
    --at-apply: "text-gray-600 hover:bg-gray-50";
}
</style>