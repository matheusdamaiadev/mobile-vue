<script setup>
import { ref, computed, onMounted } from 'vue';
import { useRouter, useRoute } from 'vue-router';
import AppHeader from '@/components/layout/AppHeader.vue';
import AppInput from '@/components/forms/AppInput.vue';
import AppButton from '@/components/forms/AppButton.vue';
import { useRecords } from '@/composables/useRecords';
import { z } from 'zod';


const router = useRouter();
const route = useRoute();
const { addRecord, getRecord, updateRecord, categories } = useRecords();

const isEditMode = computed(() => route.params.id !== 'new');

const form = ref({
    title: '',
    duration: '',
    notes: '',
    category: '',
});

onMounted(() => {
    if (isEditMode.value) {
        const record = getRecord(route.params.id);
        if (record) {
            form.value = {
                title: record.title,
                duration: record.duration,
                notes: record.notes || '',
                category: record.category || '',
            };
        } else {
            router.push('/records');
        }
    }
});
const schema = z.object({
    title: z.string().min(3, 'Mínimo 3 caracteres'),
    duration: z
        .number({ invalid_type_error: 'Duração obrigatória' })
        .min(1, 'Duração deve ser maior que 0'),
    category: z.string().min(1, 'Selecione uma categoria'),
});
const errors = ref({});

function handleSubmit() {
    errors.value = {};

    try {
        const validatedData = schema.parse({
            ...form.value,
            duration: Number(form.value.duration),
        });

        if (isEditMode.value) {
            updateRecord(route.params.id, validatedData);
        } else {
            addRecord(validatedData);
        }

        router.push('/records');

    } catch (err) {
        if (err.issues) {
            err.issues.forEach((e) => {
                errors.value[e.path[0]] = e.message;
            });
        }
    }

}


</script>

<template>
    <div>
        <AppHeader :title="isEditMode ? 'Editar Registro' : 'Novo Registro'" show-back @back="router.back()" />
        <div class="page">
            <form @submit.prevent="handleSubmit" class="form">
                <AppInput v-model="form.title" label="Título" placeholder="Ex: Estudar Vue.js" required />
                <p v-if="errors.title" class="error">
                    {{ errors.title }}
                </p>
                <AppInput v-model.number="form.duration" label="Duração (minutos)" type="number" placeholder="Ex: 60"
                    required />
                <p v-if="errors.duration" class="error">
                    {{ errors.duration }}
                </p>
                <div class="textarea-group">
                    <label class="label">Observações</label>
                    <textarea v-model="form.notes" rows="4" class="textarea"
                        placeholder="Adicione observações sobre a atividade..."></textarea>
                </div>
                <select v-model="form.category" class="select">
                    <option disabled value="">Selecione uma categoria</option>
                    <option v-for="cat in categories" :key="cat" :value="cat">
                        {{ cat }}
                    </option>
                </select>
                <p v-if="errors.category" class="error">
                    {{ errors.category }}
                </p>
                <AppButton type="submit">
                    {{ isEditMode ? 'Salvar alterações' : 'Criar registro' }}
                </AppButton>
            </form>
        </div>
    </div>
</template>

<style scoped>
.form {
    background: white;
    padding: 20px;
    border-radius: 12px;
}

.textarea-group {
    margin-bottom: 16px;
}

.label {
    display: block;
    margin-bottom: 6px;
    font-size: 14px;
    font-weight: 500;
    color: #333;
}

.textarea {
    width: 100%;
    padding: 12px 16px;
    font-size: 16px;
    border: 2px solid #ddd;
    border-radius: 8px;
    font-family: inherit;
    resize: vertical;
    transition: border-color 0.2s;
}

.textarea:focus {
    outline: none;
    border-color: #0b5cff;
}

.select {
    width: 100%;
    min-height: 48px;
    padding: 12px 16px;
    font-size: 16px;
    border: 2px solid #ddd;
    border-radius: 8px;
    background-color: white;
    font-family: inherit;
    transition: border-color 0.2s;
    margin-bottom: 16px;
}

.select:focus {
    outline: none;
    border-color: #0b5cff;
}

.error {
    color: #d93025;
    font-size: 14px;
    margin-top: 4px;
    margin-bottom: 12px;
}
</style>