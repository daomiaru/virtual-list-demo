<template>
    <div>
        {{ rowSpan }}
        <div>
            <div v-for="(variantItem, variantIndex) in formValue.variants" :key="variantItem.id"
                class="tw-p-4 tw-bg-sw-primary-color-3 tw-mb-4 tw-relative">


                <n-grid :cols="2" :x-gap="12">
                    <n-form-item-gi label="varintname">
                        <n-input v-model:value="formValue.variants[variantIndex].name" class="tw-w-20" :maxlength="50"
                            show-count />
                    </n-form-item-gi>
                </n-grid>

                <n-grid :cols="3" :x-gap="12" :y-gap="12">
                    <n-gi v-for="(valueItem, valueIndex) in variantItem.values" :key="valueIndex">
                        <n-form-item-gi :show-feedback="false" :label="valueIndex === 0 ? 'Value' : ''"
                            :show-label="valueIndex <= 2">
                            <div class="tw-flex tw-w-full">
                                <div class="tw-relative tw-flex-1">
                                    <n-input v-model:value="variantItem.values[valueIndex].val" show-count
                                        @update:value="(e) => handleValChange(e, valueItem.skuId, variantItem.id)" />
                                </div>

                                <n-button v-if="
                                    valueIndex === variantItem.values.length - 1 &&
                                    variantItem.values.length < 8
                                " class="tw-ml-4" @click="handleAddValueItem(variantIndex)">
                                    +value
                                </n-button>
                            </div>
                        </n-form-item-gi>
                    </n-gi>
                </n-grid>
            </div>

            <n-button v-if="formValue.variants.length < 3" secondary @click="handleAddVarint">
                ++ Add Variant
                ({{ formValue.variants.length }} / 3)
            </n-button>

            <div class="tw-h-6"></div>
            <!-- {{ formValue.tableData }}
    <br>
    {{ columns }}
    <div>
        <header style="display: flex;align-items: center;">
            <span v-for="(item,index) in columns" :key="index">
                {{ item.title }}|
            </span>

          
        </header>


        <main style="display: flex;margin-top: 20px;">
            <div v-for="(item,index) in columns" :key="index" style="display: flex;flex-direction: column;">
                <div v-for="(value,key) in formValue.tableData" :key="value.skuId">
                    {{ value[item.key] || '--' }}
                </div>
            </div>
        </main>
    </div> -->
            <!--       :max-height="500"
      virtual-scroll -->
            <n-data-table ref="table" class="custom-table" :columns="columns" :data="formValue.tableData"
            :max-height="500"   virtual-scroll />
            <!--       :scroll-x="1150" -->
        </div>
    </div>
</template>

<script lang="ts" setup>
import { v4 } from 'uuid'
import { ref, h } from "vue";
import { NDataTable, NInput, NImage } from "naive-ui";
import { debounce } from 'lodash-es'
const columns = ref([
    {
        key: "age",
        title: "Age",
        render() {
            return h(NInput, null)
        }
    },
    {
        key: "age6",
        title: "age6",
        render() {
            return h(NInput, null)
        }
    },
    {
        key: "age5",
        title: "age5",
        render() {
            return h(NInput, null)
        }
    },
    {
        key: "age4",
        title: "age4",
        render() {
            return h(NInput, null)
        }
    },
    {
        key: "age3",
        title: "age3",
        render() {
            return h(NInput, null)
        }
    },
    {
        key: "age2",
        title: "age2",
        render() {
            return h(NInput, null)
        }
    },
    {
        key: "age1",
        title: "age1",
        render() {
            return h(NInput, null)
        }
    },
    {
        key: "myGender",
        title: "Age",
        render(row) {
            return h(NImage, { src: row.image })
        }
    },
]);

const list = ref([])
const handleGetDefaultValueItem = () => {
    return {
        val: '',

        skuId: v4(),
    }
}
const handleGetDefaultVaritem = (index) => {
    return {
        id: v4(), // 规格id
        name: `Variant Name ${index + 1}`, // 规格名称
        values: [
            handleGetDefaultValueItem()
        ],
    }
}

const formValue = ref({
    tableData: [],
    variants: [
        handleGetDefaultVaritem(0)
    ]

})
// 获取行合并值
const rowSpan = computed<any>(() => {
    if (formValue.value.variants.length === 1) {
        return 1;
    }
    const valueLengthArr = formValue.value.variants.map(
        (item) => item.values.length,
    );
    valueLengthArr.shift();
    return valueLengthArr.reduce((a: number, b: number) => a * b);
});
const handleInit = () => {
    columns.value.unshift({
        key: formValue.value.variants[0].id,
        title: formValue.value.variants[0].name,
        // cellProps: (row, index) => {
        //     // if((index + rowSpan.value) % rowSpan.value === 0) {
        //     //     return row[formValue.value.variants[0].id]
        //     // } 
        //     return {
        //         style: {
        //             display: (index + rowSpan.value) % rowSpan.value === 0 ? 'block' : 'none'
        //         },
        //         rowSpan: (index + rowSpan.value) % rowSpan.value === 0 ? rowSpan.value : 1
        //     }
        //     // console.log((index + rowSpan.value) % rowSpan.value === 0)
        //     // return {
        //     //     rowSpan: 1,
        //     //     dataB: 123
        //     // }
        // },
        // className: (index + rowSpan.value) % rowSpan.value === 0 ? 'box' : 'nonebox',
        // render(row, index) {
        //     if ((index + rowSpan.value) % rowSpan.value === 0) {
        //         return row[formValue.value.variants[0].id]
        //     }
        //     return ''
        // },
        // rowSpan: (rowData, rowIndex) => (rowIndex + rowSpan.value) % rowSpan.value === 0 ? rowSpan.value : 1,
        // rowSpan: () => rowSpan.value,
    })
}
handleInit()
const handleUpdateTableData = () => {
    const { tableData: preData, variants } = formValue.value;
    const arr: any = [];

    const createRowData = (values: any, skuId: any) => {
        const rowData = { ...values, skuId };
        // 查找是否有相同的skuId
        // const preDataItem = preData.find((item: any) => item.skuId === skuId);
        // // // 如果有相同的skuId则取出对应的数据回显赋值
        // if(preDataItem) {
        //     rowData.image = preDataItem.image;
        // } else {
        //     rowData.image = 'https://picsum.photos/48/48'
        // }
        // if (preDataItem) {
        //   rowData.processWidth = preDataItem.processWidth;
        //   rowData.processHeight = preDataItem.processHeight;
        //   rowData.designBackgroundImage = preDataItem.designBackgroundImage;
        //   rowData.sku = preDataItem.sku;
        //   rowData.price = preDataItem.price;
        //   // 编辑状态下，保留id
        //   if (props.type === 'edit' && preDataItem?.id) {
        //     rowData.id = preDataItem.id;
        //   }
        // }
        arr.push(rowData);
    };

    const iterateVariants = (values = {}, skuId = '', index = 0) => {
        const variant = variants[index];
        if (!variant) {
            createRowData(values, skuId);
            return;
        }
        variant.values.forEach((item: any) => {
            iterateVariants(
                { ...values, [variant.id]: item.val },
                skuId + item.skuId,
                index + 1,
            );
        });
    };

    iterateVariants();
    return arr;
};

// 处理新增规格值
const handleAddValueItem = (i: number) => {
    const item = handleGetDefaultValueItem()
    formValue.value.variants[i].values.push(item);
    formValue.value.tableData = handleUpdateTableData()

};
// 处理新增规格
const handleAddVarint = () => {
    const item = handleGetDefaultVaritem(formValue.value.variants.length)
    formValue.value.variants.push(item)
    //     columns.value.unshift({
    //     key: item.id,
    //     title: item.name,
    //   })
    columns.value.splice(1, 0, {
        key: item.id,
        title: item.name,
    })
    console.log(columns.value)
    formValue.value.tableData = handleUpdateTableData()
};
const handleValChange = debounce((e, skuid, id) => {

    formValue.value.tableData.forEach((item, index) => {

        if (item.skuId.indexOf(skuid) >= 0) {

            item[id] = e
        }
    })
}, 200)
// watch(
//   () => formValue.value.variants,
//   () => {
//     console.log(123)
//    formValue.value.tableData = handleUpdateTableData();
//     //console.log(formValue.value.tableData)
//   },
//   {
//     deep: true,
//     // immediate: true,
//   },
// );
</script>
