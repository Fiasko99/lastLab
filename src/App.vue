<template>
  <div id="app">
    <div class="container">
      <div class="row">
        <div class="form-group w-100">
          <label for="name">Фамилия</label>
          <input type="text" id="name" class="form-control" v-model="name">
        </div>
        <div class="form-group w-100">
          <label for="surname">Имя</label>
          <input type="text" id="surname" class="form-control" v-model="surname">
        </div>
        <div class="form-group w-100">
          <label for="phone">Телефон</label>
          <input type="number" id="phone" class="form-control" v-model="phone">
        </div>
        <button class="btn btn-style" @click="onSave()" name="save">Сохранить</button>
        <div style="margin-left: 20px;"></div>
        <button class="btn btn-style" @click="onEdit()" name="edit">Изменить</button>
        <div style="margin-left: 20px;"></div>
        <button class="btn btn-style" @click="onDestroy()" name="delete">Удалить</button>
        <div style="margin-left: 20px;"></div>
        <p id="process">Нет активных кнопок</p>
      </div>
      <hr>
      <div class="row border-row" v-for="note in notes" :key="note.id" @click="[onRename(note.id), onDelete(note.id)]">
        <div class="col-4">{{ note.name }}</div>
        <div class="col-4">{{ note.surname }}</div>
        <div class="col-4">{{ note.phone }}</div>
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    name: 'App',
    data() {
      return {
        notes: [],
        name: "",
        surname: "",
        phone: "",
        editFlag: false,
        deleteFlag: false,
      };
    },
    components: {},
    methods: {
      updateData() {
        try {
          this.$http
            .get('http://localhost:3000/notes')
            .then((res) => res.json())
            .then((res) => (this.notes = res));
        } catch (err) {
          console.error(err);
        }
      },

      async onSave() {
        let note = {
          name: this.name,
          surname: this.surname,
          phone: this.phone,
        };
        try {
          await this.$http.post('http://localhost:3000/notes', note);
          this.updateData();
        } catch (err) {
          console.error(err);
        }
      },

      async onRename(id) {
        let note = {
          name: this.name,
          surname: this.surname,
          phone: this.phone,
        };
        try {
          if (this.editFlag) {
            await this.$http.put(`http://localhost:3000/notes/${id}`, note);
            this.updateData();
          }
        } catch (err) {
          console.error(err);
        }
      },

      onEdit() {
        this.editFlag = !this.editFlag;
        let editButton = document.getElementsByName('edit');
        editButton[0].innerHTML = this.editFlag ? "Изменение..." : "Изменить";
        let process = document.getElementById('process');
        process.innerHTML = this.editFlag ? "Выберите элемент из списка для изменения" : "Нет активных кнопок";
        let deleteSet = document.getElementsByName('delete');
        deleteSet[0].disabled = this.editFlag ? true : false;
      },

      async onDelete(id) {
        try {
          if (this.deleteFlag) {
            await this.$http.delete(`http://localhost:3000/notes/${id}`);
            this.updateData();
          }
        } catch (err) {
          console.error(err);
        }
      },

      onDestroy() {
        this.deleteFlag = !this.deleteFlag;
        let editButton = document.getElementsByName('delete');
        editButton[0].innerHTML = this.deleteFlag ? "Удаление..." : "Удалить";
        let process = document.getElementById('process');
        process.innerHTML = this.deleteFlag ? "Выберите элемент из списка для удаления" : "Нет активных кнопок";
        let editSet = document.getElementsByName('edit');
        editSet[0].disabled = this.deleteFlag ? true : false;
      },

    },
    created() {
      this.updateData();
    },
  };

</script>

<style>
  .btn-style {
    border: 1px solid #ccc;
    border-radius: 5px;
    background-color: floralwhite;
  }

  .btn-style:hover {
    border: 1px solid #80bdff;
    background-color: cornsilk;
  }

  .border-row {
    margin: 5px;
    border: 1px solid #ccc;
    border-radius: 5px;
  }

  .border-row:hover {
    border: 1px solid #80bdff;
  }

</style>
