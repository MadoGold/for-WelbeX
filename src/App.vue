<template>
  <div class="wrapper">
    <h1>Таблица</h1>
    <div class="sorting">
      <select v-model="column">
        <option value="">Выберите из списка</option>
        <option value="name">Название</option>
        <option value="quantity">Количество</option>
        <option value="distance">Расстояние</option>
      </select>
      <select v-model="condition">
        <option value="">Выберите из списка</option>
        <option value="equals">Равно</option>
        <option value="contains">Содержит</option>
        <option :disabled="column === 'name'" value="more">Больше</option>
        <option :disabled="column === 'name'" value="less">Меньше</option>
      </select>
      <input
        :type="column === 'name' ? 'text' : 'number'"
        v-model="filterValue"
      />
    </div>
    <div class="row header">
      <div class="date">Дата</div>
      <div class="name">Название</div>
      <div class="quantity">Количество</div>
      <div class="distance">Расстояние</div>
    </div>
    <div
      class="row"
      v-for="row in paginateAndSortedTable"
    >
      <div>{{ row.date }}</div>
      <div>{{ row.name }}</div>
      <div>{{ row.quantity }}</div>
      <div>{{ row.distance }}</div>
    </div>
    <div class="pagination" >
      <button v-for="pageNumber in totalPages"
              :class="{'current': pageNumber === page}"
              @click="changePage(pageNumber)"
      >{{ pageNumber }}</button>
    </div>
  </div>
</template>

<script>

export default {
  name: 'App',
  data() {
    return {
      tableRows: [
        {date: 'Mar.28.2026', name: 'sunt aut facere', quantity: 52, distance: 1073},
        {date: 'Jan.24.2025', name: 'qui est esse', quantity: 8, distance: 7235},
        {date: 'Sep.15.2027', name: 'ea molestias quasi', quantity: 25, distance: 5451},
        {date: 'Jun.09.2025', name: 'eum et est', quantity: 30, distance: 1191},
        {date: 'May.14.2027', name: 'nesciunt quas odio', quantity: 65, distance: 7551},
        {date: 'Feb.01.2023', name: 'dolorem eum magni', quantity: 55, distance: 2636},
        {date: 'Oct.10.2025', name: 'magnam facilis autem', quantity: 93, distance: 4520},
        {date: 'Jun.26.2026', name: 'dolorem dolore est', quantity: 89, distance: 9567},
        {date: 'Nov.29.2025', name: 'nesciunt iure omnis', quantity: 78, distance: 911},
        {date: 'Mar.19.2024', name: 'optio molestias id', quantity: 16, distance: 2287},
        {date: 'Feb.26.2025', name: 'et ea vero', quantity: 24, distance: 1818},
        {date: 'Feb.09.2023', name: 'in quibusdam tempore', quantity: 57, distance: 4669},
        {date: 'Apr.01.2028', name: 'dolorum ut in', quantity: 32, distance: 5773},
        {date: 'Aug.20.2023', name: 'voluptatem eligendi optio', quantity: 27, distance: 6865},
        {date: 'Jan.14.2025', name: 'eveniet quod temporibus', quantity: 67, distance: 3190},
        {date: 'Oct.11.2025', name: 'sint suscipit perspiciatis', quantity: 36, distance: 8491},
        {date: 'Jun.28.2027', name: 'fugit voluptas sed', quantity: 76, distance: 3628},
        {date: 'Jan.21.2027', name: 'voluptate et itaque', quantity: 12, distance: 1259},
        {date: 'Jan.04.2025', name: 'adipisci placeat illum', quantity: 91, distance: 5452},
        {date: 'Jan.27.2027', name: 'doloribus ad provident', quantity: 95, distance: 8195},
        {date: 'Nov.17.2024', name: 'asperiores ea ipsam', quantity: 82, distance: 8098},
        {date: 'Nov.04.2023', name: 'dolor sint quo', quantity: 9, distance: 6915},
        {date: 'Dec.19.2027', name: 'maxime id vitae', quantity: 26, distance: 8321},
        {date: 'Jul.01.2024', name: 'autem hic labore', quantity: 53, distance: 5017},
        {date: 'Sep.01.2023', name: 'rem alias distinctio', quantity: 66, distance: 8644},
        {date: 'Sep.10.2026', name: 'est et quae', quantity: 69, distance: 3695},
        {date: 'Oct.22.2023', name: 'quasi id et', quantity: 78, distance: 2263},
        {date: 'Mar.13.2026', name: 'delectus ullam et', quantity: 83, distance: 3547},
        {date: 'Apr.14.2026', name: 'iusto eius quod', quantity: 63, distance: 2251},
        {date: 'Oct.12.2023', name: 'a quo magni', quantity: 65, distance: 5273},
        {date: 'Nov.06.2024', name: 'ullam ut quidem', quantity: 44, distance: 8654},
        {date: 'Jun.15.2025', name: 'doloremque illum aliquid', quantity: 57, distance: 8382},
        {date: 'Jan.21.2025', name: 'qui explicabo molestiae', quantity: 87, distance: 6672},
        {date: 'Dec.14.2025', name: 'magnam ut rerum', quantity: 25, distance: 3826},
        {date: 'Dec.01.2024', name: 'id nihil consequatur', quantity: 53, distance: 3756},
        {date: 'Mar.20.2023', name: 'fuga nam accusamus', quantity: 77, distance: 4701},
        {date: 'Mar.21.2024', name: 'provident vel ut', quantity: 28, distance: 3778},
        {date: 'Jun.06.2023', name: 'explicabo et eos', quantity: 17, distance: 9591},
        {date: 'Aug.16.2025', name: 'eos dolorem iste', quantity: 91, distance: 4177},
        {date: 'Oct.21.2024', name: 'enim quo cumque', quantity: 43, distance: 8224},
        {date: 'Oct.29.2027', name: 'non est facere', quantity: 42, distance: 1384},
        {date: 'May.04.2024', name: 'commodi ullam sint', quantity: 38, distance: 3134},
        {date: 'Apr.20.2025', name: 'eligendi iste nostrum', quantity: 96, distance: 8316},
        {date: 'Feb.09.2023', name: 'optio dolor molestias', quantity: 25, distance: 3664},
        {date: 'Jan.08.2026', name: 'ut numquam possimus', quantity: 20, distance: 9940},
        {date: 'Jan.01.2028', name: 'aut quo modi', quantity: 13, distance: 2079},
        {date: 'Feb.04.2024', name: 'quibusdam cumque rem', quantity: 5, distance: 7426},
        {date: 'Nov.11.2023', name: 'ut voluptatem illum', quantity: 53, distance: 4637},
        {date: 'Apr.30.2026', name: 'laborum non sunt', quantity: 32, distance: 8212},
        {date: 'Aug.01.2023', name: 'repellendus qui recusandae', quantity: 19, distance: 9522},
        {date: 'Oct.31.2023', name: 'soluta aliquam aperiam', quantity: 13, distance: 9752},
        {date: 'Jan.14.2028', name: 'qui enim et', quantity: 91, distance: 8135},
        {date: 'Jan.24.2026', name: 'ut quo aut', quantity: 9, distance: 9024},
        {date: 'Mar.26.2026', name: 'sit asperiores ipsam', quantity: 37, distance: 5006},
        {date: 'Jan.25.2023', name: 'sit vel voluptatem', quantity: 75, distance: 4889},
        {date: 'Oct.29.2023', name: 'qui et at', quantity: 68, distance: 1014},
        {date: 'Apr.08.2025', name: 'sed ab est', quantity: 71, distance: 3802},
        {date: 'Sep.01.2025', name: 'voluptatum itaque dolores', quantity: 54, distance: 6674},
        {date: 'Mar.01.2025', name: 'qui commodi dolor', quantity: 57, distance: 235},
        {date: 'Feb.16.2026', name: 'consequatur placeat omnis', quantity: 37, distance: 1490},
        {date: 'Aug.15.2023', name: 'voluptatem doloribus consectetur', quantity: 97, distance: 8768},
        {date: 'Nov.18.2023', name: 'beatae enim quia', quantity: 17, distance: 6251},
        {date: 'Oct.27.2024', name: 'voluptas blanditiis repellendus', quantity: 3, distance: 1654},
        {date: 'May.09.2023', name: 'et fugit quas', quantity: 9, distance: 9957},
        {date: 'Nov.10.2022', name: 'consequatur id enim', quantity: 79, distance: 6256},
        {date: 'Apr.06.2024', name: 'repudiandae ea animi', quantity: 80, distance: 2407},
        {date: 'Feb.02.2025', name: 'aliquid eos sed', quantity: 68, distance: 7446},
        {date: 'Mar.03.2028', name: 'odio quis facere', quantity: 22, distance: 1396},
        {date: 'Jul.02.2025', name: 'fugiat quod pariatur', quantity: 1, distance: 3723},
        {date: 'Sep.09.2023', name: 'voluptatem laborum magni', quantity: 79, distance: 93},
        {date: 'Jun.15.2024', name: 'et iusto veniam', quantity: 99, distance: 5340},
        {date: 'Dec.21.2025', name: 'sint hic doloribus', quantity: 93, distance: 5705},
        {date: 'Aug.12.2027', name: 'consequuntur deleniti eos', quantity: 90, distance: 8743},
        {date: 'Dec.20.2022', name: 'enim unde ratione', quantity: 19, distance: 8361},
        {date: 'Aug.19.2026', name: 'dignissimos eum dolor', quantity: 10, distance: 3460},
        {date: 'Jan.25.2023', name: 'doloremque officiis ad', quantity: 61, distance: 508},
        {date: 'Feb.17.2023', name: 'necessitatibus quasi exercitationem', quantity: 30, distance: 3067},
        {date: 'Aug.27.2025', name: 'quam voluptatibus rerum', quantity: 46, distance: 1487},
        {date: 'Oct.14.2023', name: 'pariatur consequatur quia', quantity: 49, distance: 3752},
        {date: 'Mar.04.2023', name: 'labore in ex', quantity: 79, distance: 7561},
        {date: 'Dec.17.2023', name: 'tempora rem veritatis', quantity: 47, distance: 864},
        {date: 'May.02.2023', name: 'laudantium voluptate suscipit', quantity: 25, distance: 4606},
        {date: 'Aug.27.2025', name: 'odit et voluptates', quantity: 40, distance: 5867},
        {date: 'Aug.28.2023', name: 'optio ipsam molestias', quantity: 59, distance: 5670},
        {date: 'Sep.10.2027', name: 'dolore veritatis porro', quantity: 89, distance: 4212},
        {date: 'Jul.26.2027', name: 'placeat quia et', quantity: 64, distance: 4139},
        {date: 'Dec.03.2025', name: 'nostrum quis quasi', quantity: 67, distance: 5233},
        {date: 'Mar.01.2027', name: 'sapiente omnis fugit', quantity: 76, distance: 7101},
        {date: 'Sep.07.2026', name: 'sint soluta et', quantity: 93, distance: 7219},
        {date: 'Sep.08.2027', name: 'ad iusto omnis', quantity: 64, distance: 5239},
        {date: 'Jan.20.2027', name: 'aut amet sed', quantity: 28, distance: 5597},
        {date: 'Jun.29.2023', name: 'ratione ex tenetur', quantity: 38, distance: 2061},
        {date: 'Sep.10.2025', name: 'beatae soluta recusandae', quantity: 30, distance: 134},
        {date: 'Sep.01.2025', name: 'qui qui voluptates', quantity: 49, distance: 1881},
        {date: 'Nov.15.2025', name: 'id minus libero', quantity: 64, distance: 3828},
        {date: 'Sep.20.2027', name: 'quaerat velit veniam', quantity: 25, distance: 576},
        {date: 'Sep.30.2027', name: 'quas fugiat ut', quantity: 16, distance: 5104},
        {date: 'Sep.03.2027', name: 'laboriosam dolor voluptates', quantity: 5, distance: 3065},
        {date: 'May.29.2023', name: 'temporibus sit alias', quantity: 24, distance: 4714},
        {date: 'Feb.17.2028', name: 'at nam consequatur', quantity: 57, distance: 9464},
      ],
      column: '',
      condition: '',
      filterValue: '',
      page: 1,
      pagesLimit: 10,
    }
  },
  methods: {
    changePage(pageNum) {
      this.page = pageNum
    }
  },
  computed: {
    sortedRows() {
      return this.tableRows.filter(row => {
        switch (this.condition) {
          case 'more': return row[this.column] > +this.filterValue;
          case 'less': return row[this.column] < +this.filterValue;
          case 'equals': return row[this.column] === (isNaN(this.filterValue)
            ? this.filterValue
            : +this.filterValue);
          case 'contains': return row[this.column].toString().includes(this.filterValue);
          default: return true
        }
      })
    },
    paginateAndSortedTable() {
      const start = (this.page - 1) * this.pagesLimit;
      const end = start + this.pagesLimit;
      return this.sortedRows.slice(start, end);
    },
    totalPages() {
      return Math.ceil(this.sortedRows.length / this.pagesLimit);
    }
  },
  watch: {
    column() {
      if (this.column === 'name') this.condition = '';
    }
  }
}
</script>

<style lang="scss">
* {
  padding: 0;
  margin: 0;
  box-sizing: border-box;
}

#app, body {
  height: 100vh;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #232526;
  background-color: #a3adb7;
  font-size: 24px;
}

.wrapper {
  margin: 0 auto;
  padding: 100px 0;
  max-width: 1100px;
}

.row {
  margin-top: 5px;
  display: flex;
  gap: 5px;

  &:hover {
    div {
      background-color: #7c8b9d;
    }
  }

  div {
    padding: 5px;
    background-color: #81888f;
    flex: 1 1 25%;

    &:nth-child(2) {
      flex: 1 1 50%;
    }
  }
}

.header {
  cursor: pointer;

  &:hover div {
    background-color: #81888f;
  }
}

.sorting {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 20px;
}

.pagination {
  margin-top: 10px;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 5px;

  button {
    padding: 5px 10px;
    font-size: 20px;
    border: 1px solid #7c8b9d;
    cursor: pointer;
    background-color: lightcyan;

    &.current {
      background-color: #94d2d2;
    }
  }
}
</style>
