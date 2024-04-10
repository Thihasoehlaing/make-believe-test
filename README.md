# Make Believe Test

## Test 1: Layout HTML/CSS
> Reference [Image](image.png) 

### Technology
1. HTML
2. CSS

## Test 2: Frontend Programming / Table Component
> Reference Table UI from Figma

### Technology
1. Vue
2. Vite
3. TailwindCss
4. [CSS Loader (Continuous Style)](https://css-loaders.com/continuous/)

### Code Reference
[PrimeVue Datatable](https://primevue.org/datatable/)

### Vue Table Documentation

```
//example
<script setup>
  import FooTable from '@/components/FooTable.vue'
</script>

<template>
	<div class="bg-white p-5 container mx-auto">
		<FooTable
			:groupTable="true"
			:json="simpleData"
			groupKey="team"
			:loading="loading"
			v-if="reload"
		></FooTable>
</template>
```

> Explaination

- v-if="reload" is to render after fetching data is done.
- loading is for template loader.
- groupTable is boolean property to choose group table or simple table?
- groupKey is also string property if table is group table
- json is for data. Table is formatted by data format. It can be checked in component file.