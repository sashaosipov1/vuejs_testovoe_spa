<script>
const dateNow = new Date(Date.now()).toLocaleString("ru-RU", {});
export default {
  data() {
    return {
      dataForTesting: [
        {
          date: dateNow,
          name: 'name1',
          quantity: 10,
          distance: 99
        },
        {
          date: dateNow,
          name: 'Арбузы',
          quantity: 20,
          distance: 150
        },
        {
          date: dateNow,
          name: 'имя2',
          quantity: 41,
          distance: 1
        },
        {
          date: dateNow,
          name: 'Кирпичи',
          quantity: 1,
          distance: 1777
        },
        {
          date: dateNow,
          name: 'name112',
          quantity: 10,
          distance: 99
        },
        {
          date: dateNow,
          name: 'Кобласа',
          quantity: 20,
          distance: 150
        },
        {
          date: dateNow,
          name: 'Артём',
          quantity: 41,
          distance: 1
        },
        {
          date: dateNow,
          name: 'Шишки',
          quantity: 111,
          distance: 777
        },
        {
          date: dateNow,
          name: 'Сыр',
          quantity: 1990,
          distance: 99111
        },
        {
          date: dateNow,
          name: 'Кукуруза',
          quantity: 201,
          distance: 15044
        }
      ],
      order: -1,
      selectedColumn: '',
      filterByColumn: [
        {
          columnName: 'Название',
          value: 'name'
        },
        {
          columnName: 'Количество',
          value: 'quantity'
        },
        {
          columnName: 'Расстояние',
          value: 'distance'
        },
      ],
      selectedCondition: '',
      filterByCondition: [
        {
          conditionName: 'Равно',
          value: 1
        }, {
          conditionName: 'Содержит',
          value: 2
        }, {
          conditionName: 'Больше',
          value: 3
        }, {
          conditionName: 'Меньше',
          value: 4
        },
      ],
      filterÏnputValue: '',
      pageNumber: 0,
      size: 3,
      pagArray: [],
      filtersWarn: false
    }
  },
  methods: {
    sortBy: function (event, field) {
      let or = this.order;
      event.target.dataset.order = -or;
      for (const cell of event.target.parentNode.cells) {
        cell.classList.toggle('sorted', cell === event.target);
      }

      this.dataForTesting.sort(function (a, b) {
        if (a[field] < b[field]) {
          return or;
        }

        if (a[field] > b[field]) {
          return -(or);
        }

        return 0;
      });

      this.order = -or;
    },
    filteredValues: function () {
      if (!this.filterÏnputValue) {
        this.filtersWarn = false;
        return this.dataForTesting;
      }

      if (this.selectedColumn === 'name' && this.selectedCondition == 2) {
        if (this.filterÏnputValue) {
          this.filtersWarn = false;
          return this.dataForTesting.filter((item) => {
            return this.filterÏnputValue.toLowerCase().split('').every(v => item.name.toLowerCase().includes(v))
          })
        } else {
          return this.dataForTesting;
        }
      }

      if (this.selectedColumn != 'name') {
        if (this.filterÏnputValue) {
          this.filtersWarn = false;
          if (this.selectedCondition == 1) {
            return this.dataForTesting.filter((item) => {
              return this.filterÏnputValue == item[this.selectedColumn];
            })
          }

          if (this.selectedCondition == 2) {
            return this.dataForTesting.filter((item) => {
              let e = item[this.selectedColumn].toString();
              return this.filterÏnputValue.toLowerCase().split('').every(v => e.includes(v))
            })
          }

          if (this.selectedCondition == 3) {
            return this.dataForTesting.filter((item) => {
              return this.filterÏnputValue < item[this.selectedColumn];
            })
          }

          if (this.selectedCondition == 4) {
            return this.dataForTesting.filter((item) => {
              return this.filterÏnputValue > item[this.selectedColumn];
            })
          }
        } else {
          return this.dataForTesting;
        }
      }

      if (!this.selectedColumn && !this.selectedCondition) {
        this.filtersWarn = true;
        return [];
      }

      else {
        this.filtersWarn = true;
        return [];
      }
    },
    nextPage() {
      this.pageNumber++;
    },
    prevPage() {
      this.pageNumber--;
    },
    paginatedData() {
      const start = this.pageNumber * this.size,
        end = start + this.size;
      return this.filteredValues().slice(start, end);
    },
    pageCount() {
      let l = this.filteredValues().length,
        s = this.size;
      return Math.ceil(l / s);
    },
    goPage(num) {
      this.pageNumber = num - 1;
    },
    fillPagArr() {
      this.pagArray = [];
      for (let index = 0; index < this.pageCount(); index++) {
        this.pagArray.push(index + 1);
      }
    }
  },
  mounted: function () {
    this.fillPagArr();
  },
  beforeUpdate() {
    this.fillPagArr();
  }
}
</script>

<template>
  <div class="container">
    <div class="">

      <select name="" id="" v-model="selectedColumn" class="m-10">
        <option v-for="col in filterByColumn" :value='col.value'>{{col.columnName}}</option>
      </select>

      <select name="" id="" v-model="selectedCondition" class="m-10">
        <option v-for="con in filterByCondition" :value="con.value">{{con.conditionName}}</option>
      </select>

      <input type="text" placeholder="Текст введите..." v-model="filterÏnputValue" class="m-10">

      <span v-if="filtersWarn" class="danger">Несоответствующие фильтры!!</span>
    </div>
    <div class="row">
      <table class="table" style="height: 164px;">
        <thead>
          <tr>
            <th scope="col">Дата</th>
            <th scope="col" v-on:click="sortBy($event, 'name')">Название</th>
            <th scope="col" v-on:click="sortBy($event, 'quantity')">Количество</th>
            <th scope="col" v-on:click="sortBy($event, 'distance')">Расстояние</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="data in paginatedData()">
            <td>{{data.date}}</td>
            <td>{{data.name}}</td>
            <td>{{data.quantity}}</td>
            <td>{{data.distance}}</td>
          </tr>
        </tbody>
      </table>
    </div>
    <div class="" style="display: flex;">
      <button @click="prevPage" :disabled="pageNumber==0" class="m-10">
        Previous
      </button>
      <div>
        <button class="pag_item" v-for="item in pagArray" v-on:click="goPage(item)" :disabled="pageNumber == item-1">
          {{item}}
        </button>
      </div>
      <button @click="nextPage" :disabled="pageNumber>=pageCount()-1" class="m-10">
        Next
      </button>
    </div>
  </div>
</template>

<style scoped>
.m-10 {
  margin: auto 10px;
}
</style>