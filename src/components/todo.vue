<template>
  <div>
    <section>
      <h2>
        Todo
      </h2>
      <input
        @keyup.enter="push_list"
        v-model="newTodo"
      >
      <br><br>
      <input v-model="alldone" type="checkbox">
      <span>全选|</span>
      <span @click="changeV('all')">全部list</span>
      <span @click="changeV('done')">已完成</span>
      <span @click="changeV('plan')">未完成</span>
    </section>
    <ul>
      <li
        v-for="i in lists"
        :class="{editing: i == editedTodo }"
      >
        <div class="viewtodo">
          <input type="checkbox"
                 v-model="i.m_checked"
                 class="m_checkbox">
          <span @dblclick="editTodo(i)">{{ i.title }}</span>
          <span class="removetodo" @click=removetodo(i)>x</span>
        </div>

        <div class="view">
          <input class="edit"
                 v-onfocus="i == editedTodo"
                 v-model="i.title"
                 @blur="doneEdit(i)"
                 @keyup.enter="doneEdit(i)"
                 @keyup.esc="cancelEdit(i)"
          >
        </div>
      </li>
    </ul>
  </div>
</template>

<script>
  import baseCss from "../assets/base.less"
  export default {
    name: 'todo',
    data () {
      return {
        newTodo: "",
        todoList: [],
        m_checked: "",
        editedTodo: "null",
        selectedV: "all"
      }
    },
    methods: {
      push_list: function () {
        let _this = this
        var val = this.newTodo
        if (!val) {
          return
        }
        this.todoList.push({
          title: val,
          m_checked: false
        })
        this.newTodo = ""
      },
      removetodo: function (todo) {
        this.todoList.splice(this.todoList.indexOf(todo), 1)
      },
      editTodo: function (edit_i) {
        this.beforeEdit = edit_i.title
        this.editedTodo = edit_i
      },
      doneEdit: function (_i) {
        if (!this.editedTodo) {
          return
        }
        this.editedTodo = null
      },
      cancelEdit: function (_e) {
        _e.title = this.beforeEdit
        this.editedTodo = null
      },
      changeV: function(V){
        this.selectedV = V
      }
    },
    directives: {
      onfocus: function (el, value) {
        if (value) {
          el.focus()
        }
      }
    },
    computed: {
      alldone: {
        get: function () {
          return this.todoList.some(item => item.m_checked)
        },
        set: function (value) {
          this.todoList.forEach(function (i) {
            i.m_checked = value
          })
        }
      },
      lists:function(){
        var filters = {
          all: function (v) {
            return v
          },
          plan: function (v) {
            return v.filter(function (t) {
              return !t.m_checked
            })
          },
          done: function (v) {
            return v.filter(function (t) {
              return t.m_checked
            })
          }
        }
        return filters[this.selectedV](this.todoList)
      }
    }


  }
</script>


