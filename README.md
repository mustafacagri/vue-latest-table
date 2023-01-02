# VueLatestTable

This repo aims to use a data table that is supported by the `latest Vue version`. I generally use the vuetify for the data table components however currently its data table is not compatible with `Vue 3`. That's why I thought to create this repo to use for my own purpose actually.

## Intallation

- `npm i vue-latest-table`
- `npm i`

that's all.

## Usage

in the setup section

`import VueLatestTable from 'VueLatestTable'`

in the template section

```
  <VueLatestTable
    :headers="headers" // the header
    :data="desserts" // the data
    :isSearchable="true" // it adds the search box
    searchPlaceholder="Search" // the placeholder in the search box
    :footer="{
      rowsPerPage: [3, '1', 'dsas', 10, 20, -1], // we only use the numbers, if there is a typo, we skip it. -1 means All
      allText: 'ALL' // for translation purposes
    }"
    :defaultTheme="true"
    noData="Sorry, there is no data to show for now..." // for translation purposes
    rowsPerPageText="Rows per page" // for translation purposes
  />
```

### Github & npmjs

- https://github.com/mustafacagri/vue-latest-table
- https://www.npmjs.com/package/vue-latest-table

### Example Data

```
  const headers = [
    {
      text: 'Dessert (100g serving)',
      align: 'start',
      filterable: false,
      value: 'name'
    },
    { text: 'Calories', value: 'calories' },
    { text: 'Fat (g)', value: 'fat' },
    { text: 'Carbs (g)', value: 'carbs' },
    { text: 'Protein (g)', value: 'protein' },
    { text: 'Iron (%)', value: 'iron' }
  ]
```

```
  const desserts = [
    {
      name: 'Frozen Yogurt',
      calories: 159,
      fat: 6.0,
      carbs: 24,
      protein: 4.0,
      iron: '1%'
    },
    {
      name: 'Ice cream sandwich',
      calories: 237,
      fat: 9.0,
      carbs: 37,
      protein: 4.3,
      iron: '1%'
    },
    {
      name: 'Eclair',
      calories: 262,
      fat: 16.0,
      carbs: 23,
      protein: 6.0,
      iron: '7%'
    },
    {
      name: 'Cupcake',
      calories: 305,
      fat: 3.7,
      carbs: 67,
      protein: 4.3,
      iron: '8%'
    },
    {
      name: 'Gingerbread',
      calories: 356,
      fat: 16.0,
      carbs: 49,
      protein: 3.9,
      iron: '16%'
    },
    {
      name: 'Jelly bean',
      calories: 375,
      fat: 0.0,
      carbs: 94,
      protein: 0.0,
      iron: '0%'
    },
    {
      name: 'Lollipop',
      calories: 392,
      fat: 0.2,
      carbs: 98,
      protein: 0,
      iron: '2%'
    },
    {
      name: 'Honeycomb',
      calories: 408,
      fat: 3.2,
      carbs: 87,
      protein: 6.5,
      iron: '45%'
    },
    {
      name: 'Donut',
      calories: 452,
      fat: 25.0,
      carbs: 51,
      protein: 4.9,
      iron: '22%'
    },
    {
      name: 'KitKat',
      calories: 518,
      fat: 26.0,
      carbs: 65,
      protein: 7,
      iron: '6%'
    },
    {
      name: 'Donut',
      calories: 452,
      fat: 25.0,
      carbs: 51,
      protein: 4.9,
      iron: '22%'
    }
  ]
```

## Visualization

```
  #vueLatestTable {
    /* we can implement some CSS for this "Vue Latest Table" here */
    margin: 0 auto;
    margin-top: 30px;

    .searchBox {
      color: #333 !important;
    }
  }
```

You can implement some styles for the table in your App.vue or the page where you use it.

### 📷 Screenshots
