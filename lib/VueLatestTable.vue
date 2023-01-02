<script setup>
import { computed, onBeforeMount, ref, watch } from 'vue'

const props = defineProps({
  headers: {
    type: Array,
    required: true
  },
  data: {
    type: Array,
    required: true
  },
  isSearchable: Boolean,
  searchPlaceholder: String,
  footer: { type: Object, default: {} },
  defaultTheme: Boolean,
  noData: {
    type: String,
    default: 'No data found'
  },
  rowsPerPageText: { type: String, default: 'Rows per page' }
})

const STRINGS = {
  allText: 'All'
}
const defaultRowsPerPage = [10, 25, 50, -1]

const tempData = ref([])
const currentPage = ref(1)

const showingRange = computed(() => {
  const start = rowsPerPage.value * (currentPage.value - 1) + 1
  let end = currentPage.value * rowsPerPage.value

  if (end > showingTotalRecords.value || end < 0) {
    end = showingTotalRecords.value
  }

  return { start, end }
})

const showingTotalRecords = ref(0)
const searchBox = ref('')
// const searchableFields = ref([])

const footer = props.footer || {} // it will be an empty object {} by default
let showedData = ref([])
let rowsPerPage = ref(defaultRowsPerPage[0])

const totalPages = computed(() => {
  return showingTotalRecords.value % rowsPerPage.value === 0
    ? showingTotalRecords.value / rowsPerPage.value
    : Math.trunc(showingTotalRecords.value / rowsPerPage.value) + 1
})

const updateRowsPerPage = (
  pageSize = rowsPerPage.value,
  data = props.data,
  search = searchBox?.value,
  fromSearch = false
) => {
  // by default, we assign the rowsPerPage to page if the page is empty
  let allSelected = false

  if (!fromSearch && search) {
    data = findInValues(data, search)
  }

  tempData.value = data

  showingTotalRecords.value = data.length

  if (pageSize === -1) {
    pageSize = data.length
    allSelected = true
  }

  if (pageSize && !isNaN(parseInt(pageSize))) {
    // check the pageSize number is a number or not, if not, make the rowsPerPage first element of the footer.rowsPerPage
    rowsPerPage.value = pageSize
  } else {
    rowsPerPage.value = footer.rowsPerPage[0]
  }

  showedData.value = data.slice(0, pageSize)

  if (allSelected) {
    rowsPerPage.value = -1 // we selected the all in the dropdown again
  }
}

const findInValues = (arr, value) => {
  value = String(value).toLowerCase()

  return arr.filter(o => Object.entries(o).some(entry => String(entry[1]).toLowerCase().includes(value)))
}

watch(
  () => searchBox.value,
  (newData, _oldData) => {
    const data = findInValues(props.data, newData)

    updateRowsPerPage(rowsPerPage.value, data, newData, true)
  }
)

const changePage = (page = currentPage.value) => {
  currentPage.value = page

  const start = (page - 1) * rowsPerPage.value
  const rows = isNaN(parseInt(rowsPerPage.value)) ? 1 : parseInt(rowsPerPage.value)

  showedData.value = tempData.value.slice(start, start + rows)
}

onBeforeMount(() => {
  // if (props?.isSearchable) {
  //   searchableFields.value = props.headers.map(item => item.value)
  // }

  if (!Array.isArray(footer.rowsPerPage)) {
    footer.rowsPerPage = []
  } else {
    footer.rowsPerPage = footer.rowsPerPage.map(p => parseInt(p)) // we parse the pages here
    footer.rowsPerPage = footer.rowsPerPage.filter(p => !isNaN(p)) // if it is not a Number, we will remobe the page from the list
  }

  if (!footer?.rowsPerPage || footer.rowsPerPage.length === 0) {
    footer['rowsPerPage'] = [10, 25, 50, -1]
  }

  rowsPerPage.value = footer.rowsPerPage[0]

  updateRowsPerPage(rowsPerPage.value) // excute it initially

  if (!footer?.allText) {
    footer.allText = STRINGS.allText
  }
})
</script>

<template>
  <div id="vueLatestTable" :class="defaultTheme ? 'defaultTheme' : ''">
    <div id="isSearchable">
      <!-- <select multiple class="searchableFields" size="1">
        <option v-for="field in searchableFields" :key="field.id">{{ field }}</option>
      </select> -->
      <input
        type="text"
        class="searchBox"
        :placeholder="searchPlaceholder ? searchPlaceholder : ''"
        v-model="searchBox"
      />
    </div>
    <table aria-hidden="true">
      <thead>
        <tr>
          <th v-for="header in headers" :key="header.value">{{ header.text }}</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="row in showedData" :key="row.id">
          <td v-for="header in headers" :key="header.value">{{ row[header.value] }}</td>
        </tr>
      </tbody>
    </table>
    <div v-if="showedData.length === 0" class="noData">{{ noData }}</div>
    <footer>
      <div>
        <template v-if="footer?.rowsPerPage">
          {{ rowsPerPageText }}:
          <select class="rowsPerPage" v-model="rowsPerPage" @change="updateRowsPerPage()">
            <template v-for="(page, index) in footer.rowsPerPage" :key="page.id">
              <option v-if="page === -1" value="-1">{{ footer.allText }}</option>
              <template v-else>
                <option v-if="data.length >= page" :selected="index === 0">{{ page }}</option>
              </template>
            </template>
          </select>
        </template>
      </div>
      <div>
        <p>{{ showingRange.start }} - {{ showingRange.end }} of {{ showingTotalRecords }}</p>
        <p class="arrows">
          <label v-if="currentPage !== 1" @click="changePage(currentPage - 1)">←</label>
          <label v-if="totalPages > 1">
            <select v-model="currentPage" @change="changePage()">
              <option v-for="page in totalPages" :key="page.id">{{ page }}</option>
            </select>
          </label>
          <label v-if="currentPage < totalPages" @click="changePage(currentPage + 1)" class="ml">→</label>
        </p>
      </div>
    </footer>
  </div>
</template>

<style scoped lang="scss">
#vueLatestTable {
  display: block;
  width: 100%;
  padding: 10px;

  #isSearchable {
    display: flex;
    margin-bottom: 20px;

    .searchableFields {
      flex-grow: 1;
      margin-right: 20px;
      width: calc(100% - 20px);
      padding: 10px 10px;
    }
    .searchBox {
      flex-grow: 1;
      display: block;
      width: calc(100% - 20px);
      padding: 10px 10px;
    }
  }

  table {
    width: 100%;
    border-spacing: 0;

    th {
      text-align: left;
      padding: 5px 10px;
    }

    tr {
      &:hover {
        background-color: #eee;
      }

      td {
        padding: 5px 10px;
      }
    }
  }

  footer {
    justify-content: space-between;
    padding-left: 10px;
    margin-top: 20px;
    display: flex;

    & > div {
      flex-grow: 1;
    }

    & > div:last-child {
      text-align: right;
      display: flex;
      justify-content: flex-end;
      margin-top: -10px;

      p:first-child {
        text-align: left !important;
      }

      p {
        text-align: right;
        margin-right: 10px;
      }

      .arrows {
        margin-top: 10px;
        .ml {
          margin-left: 10px;
        }
      }
    }

    select {
      padding: 5px 10px;
      margin-left: 10px;
    }
  }

  .noData {
    text-align: center;
    margin: 20px 0;
  }
}
.defaultTheme {
  border: 1px solid #aaa;
  border-radius: 10px;
}
</style>
