<template>
  <q-page class="row q-pt-xl">
    <div class="full-width q-px-xl">
      <div class="q-mb-xl">
        <form @submit.prevent.stop="onSubmit">
          <q-input
            ref="nameRef"
            v-model="inputData.name"
            label="姓名"
            filled
            lazy-rules
            :rules="[(val) => !!val || '請輸入姓名']"
          />
          <q-input
            ref="ageRef"
            v-model="inputData.age"
            label="年齡"
            filled
            lazy-rules
            :rules="[(val) => !!val || '請輸入年齡']"
          />
          <q-btn color="primary" class="q-mt-md" type="submit">新增</q-btn>
        </form>
      </div>

      <q-table
        flat
        bordered
        ref="tableRef"
        :rows="blockData"
        :columns="(tableConfig as QTableProps['columns'])"
        row-key="id"
        hide-pagination
        separator="cell"
        :rows-per-page-options="[0]"
      >
        <template v-slot:header="props">
          <q-tr :props="props">
            <q-th v-for="col in props.cols" :key="col.name" :props="props">
              {{ col.label }}
            </q-th>
            <q-th></q-th>
          </q-tr>
        </template>

        <template v-slot:body="props">
          <q-tr :props="props">
            <q-td
              v-for="col in props.cols"
              :key="col.name"
              :props="props"
              style="min-width: 120px"
            >
              <div>{{ col.value }}</div>
            </q-td>
            <q-td class="text-right" auto-width v-if="tableButtons.length > 0">
              <q-btn
                @click="handleClickOption(btn, props.row)"
                v-for="(btn, index) in tableButtons"
                :key="index"
                size="sm"
                color="grey-6"
                round
                dense
                :icon="btn.icon"
                class="q-ml-md"
                padding="5px 5px"
              >
                <q-tooltip
                  transition-show="scale"
                  transition-hide="scale"
                  anchor="top middle"
                  self="bottom middle"
                  :offset="[10, 10]"
                >
                  {{ btn.label }}
                </q-tooltip>
              </q-btn>
            </q-td>
          </q-tr>
        </template>
        <template v-slot:no-data="{ icon }">
          <div
            class="full-width row flex-center items-center text-primary q-gutter-sm"
            style="font-size: 18px"
          >
            <q-icon size="2em" :name="icon" />
            <span> 無相關資料 </span>
          </div>
        </template>
      </q-table>
    </div>
    <q-dialog v-model="prompt" persistent>
      <q-card style="min-width: 350px">
        <q-card-section>
          <div class="text-h6">編輯欄位</div>
        </q-card-section>

        <q-card-section class="q-pt-none">
          <q-input
            dense
            v-model="promptData.name"
            autofocus
            @keyup.enter="prompt = false"
          />
          <q-input
            dense
            v-model="promptData.age"
            autofocus
            @keyup.enter="prompt = false"
          />
        </q-card-section>

        <q-card-actions align="right" class="text-primary">
          <q-btn flat label="確認修改" v-close-popup />
        </q-card-actions>
      </q-card>
    </q-dialog>
  </q-page>
</template>

<script setup lang="ts">
import axios from 'axios';
import { QTableProps } from 'quasar';
import { ref } from 'vue';
interface btnType {
  label: string;
  icon: string;
  status: string;
}
const nameRef = ref(null);
const ageRef = ref(null);
const prompt = ref(false);

const blockData = ref([
  {
    name: 'test',
    age: 25,
  },
]);
const promptData = ref({
  name: '',
  age: '',
});
const tableConfig = ref([
  {
    label: '姓名',
    name: 'name',
    field: 'name',
    align: 'left',
  },
  {
    label: '年齡',
    name: 'age',
    field: 'age',
    align: 'left',
  },
]);
const tableButtons = ref([
  {
    label: '編輯',
    icon: 'edit',
    status: 'edit',
  },
  {
    label: '刪除',
    icon: 'delete',
    status: 'delete',
  },
]);

const inputData = ref({
  name: '',
  age: '',
});
function handleClickOption(btn: btnType, data: any) {
  if (btn.status === 'edit') {
    prompt.value = true;
    promptData.value = data;
  }

  if (btn.status === 'delete') {
    blockData.value = blockData.value.filter((item) => {
      return item !== data;
    });
  }
}
function onSubmit() {
  nameRef.value.validate();
  ageRef.value.validate();
  if (nameRef.value.hasError || ageRef.value.hasError) {
    // form has error
  } else {
    const { name, age } = inputData.value;
    blockData.value.push({ name, age: parseInt(age) });
  }
}
</script>

<style lang="scss" scoped>
.q-table th {
  font-size: 20px;
  font-weight: bold;
}

.q-table tbody td {
  font-size: 18px;
}
</style>
