<template>
    <div class="w-full">
        <table class="w-full">
            <thead>
                <tr class="border-b-2 border-black">
                    <template v-if="shouldDisplayGroupKeyColumn">
                        <th
                            v-for="(header, index) in json.header.data"
                            :key="index"
                            v-if="index !== groupKeyIndex"
                            class="py-3 capitalize px-2 md:px-3 whitespace-nowrap"
                            :class="getAttr(index, json.header, 'class')"
                        >
                        {{ header }}
                        </th>
                    </template>
                    <template v-else>
                    <!-- Render other columns -->
                        <th
                            v-for="(header, index) in getMobileHeader.data"
                            :key="index"
                            class="py-3 capitalize px-2 md:px-3 whitespace-nowrap"
                            :class="getAttr(index, getMobileHeader, 'class')"
                        >
                            {{ header }}
                        </th>
                    </template>
                </tr>
            </thead>
            <tbody v-if="groupTable" class="bg-[#E5E7EB]">
                <template v-if="!loading">
                    <template 
                        v-for="(group, groupValue, groupIndex) in groupedData" 
                        v-if="groupTable && shouldDisplayGroupKeyColumn"
                    >
                        <tr 
                            v-for="(item, itemIndex) in group" 
                            :key="itemIndex"
                            :class="[groupIndex % 2 ? 'bg-white' : 'bg-[#ese7eb]']"
                        >
                            <template v-if="itemIndex === 0">
                                <td 
                                    :rowspan="group.length" 
                                    class="px-2 md:px-3 py-2 whitespace-nowrap text-sm font-medium"
                                >
                                    <div v-html="groupValue"></div>
                                </td>
                            </template>
                            <td
                                v-for="(value, key, i) in item"
                                :key="i"
                                class="px-2 md:px-3 py-2 whitespace-nowrap text-sm font-medium border-[#D1D5DB]"
                                :class="[itemIndex > 0 ? 'border-t' : '']"
                            >
                                <div v-html="value"></div>
                            </td>
                        </tr>
                    </template>
                    <template 
                        v-for="(group, groupValue, groupIndex) in groupedData"
                        :key="groupValue"
                        v-if="groupTable && !shouldDisplayGroupKeyColumn"
                    >
                        <!-- Group key as row subheader -->
                        <tr :class="groupIndex % 2 === 0 ? 'bg-white' : 'bg-[#E5E7EB]'">
                            <td
                                :colspan="getMobileHeader.data.length"
                                class="py-2 px-2 md:px-3 whitespace-nowrap text-sm font-medium"
                            >
                                {{ groupValue }}
                            </td>
                        </tr>
                        <!-- Grouped data rows -->
                        <tr
                            v-for="(item, itemIndex) in group"
                            :key="itemIndex"
                            :class="groupIndex % 2 === 0 ? 'bg-white' : 'bg-[#E5E7EB]'"
                        >
                            <td
                                v-for="(value, key) in item"
                                :key="key"
                                class="px-2 md:px-3 py-2 whitespace-nowrap text-sm font-medium border-[#D1D5DB]"
                                :class="[itemIndex % 2 ? 'border-t' : '']"
                            >
                                <div v-html="value"></div>
                            </td>
                        </tr>
                    </template>
                </template>
                <tr v-else>
                    <td :colspan="json.header.data.length" class="bg-gray-200">
                        <div class="loader"></div>
                    </td>
                </tr>
            </tbody>
            <tbody v-else class="bg-[#E5E7EB]">
                <template v-if="!loading">
                    <tr v-for="(item, index) in data" :key="index">
                        <td 
                            v-for="(value, key) in item" 
                            :key="key" 
                            class="px-4 py-2"
                        >
                            <div v-html="value"></div>
                        </td>
                    </tr>
                </template>
                <tr v-else>
                    <td :colspan="json.header.data.length" class="bg-gray-200">
                        <div class="loader"></div>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
</template>
  
<script>
    export default {
        props: {
            loading:{
                type: Boolean,
                required: false,
                default: false,
            },
            json: {
                type: Object,
                required: true,
                default: {}
            },
            groupKey: {
                type: String,
                default: "Category"
            },
            groupTable: {
                type: Boolean,
                default: false
            }
        },
        data() {
            return {
                viewportWidth: window.innerWidth,
                modifiedKeys: []
            };
        },
        computed: {
            shouldDisplayGroupKeyColumn() {
                return this.viewportWidth >= 1440;
            },
            groupKeyIndex() {
                // Find the index of the group key column in the array of headers
                return this.json.header.data.findIndex(header => header === this.groupKey);
            },
            getMobileHeader(){
                if(this.viewportWidth < 1440){
                    
                    let header = {
                        data: this.json.header.data.filter(item => item !== this.groupKey),
                        attr: this.json.header.attr
                    };
                    this.modifiedKeys = this.json.keys.filter(item => item !== this.groupKey)
                    delete header.attr[this.groupKey];
                    return header;
                } else {
                    this.modifiedKeys = this.json.keys
                    return this.json.header;
                }
            },
            groupedData() {
                if (!this.groupTable) {
                    // If groupTable is false, return undefined
                    return undefined;
                }
                const grouped = {};
                this.json.body.data.forEach(item => {
                    const groupValue = item[this.groupKey];
                    
                    if (!grouped[groupValue]) {
                        grouped[groupValue] = [];
                    }
                    
                    const dataToPush = {};
                    Object.keys(item).forEach((key) => {
                        if (key !== this.groupKey) {
                            dataToPush[key] = item[key];
                        }
                    });
                    grouped[groupValue].push(dataToPush);
                });
                return grouped;
            }
        },
        mounted() {
            this.modifiedKeys = this.json.keys
            // Add an event listener for the window resize event
            window.addEventListener('resize', this.handleResize);
        },
        beforeUnmount() {
            // Remove the event listener before component is destroyed
            window.removeEventListener('resize', this.handleResize);
        },
        methods:{
            getAttr (current, list, key){
                if (list.attr) {
                    let item;
                    item = list.attr[this.modifiedKeys[current]];
                    if (item) {
                        return item[key] ? item[key] : "";
                    }
                }
                return "";
            },
            handleResize() {
                // Update the viewportWidth when the window is resized
                this.viewportWidth = window.innerWidth;
                if(window.innerWidth > 1440){
                    this.modifiedKeys = this.json.keys
                }
            }
        }
    };
</script>
  
<style scoped>
    
</style>
  