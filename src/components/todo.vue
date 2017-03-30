<template>
  <div>
    <section>
      <h2>
        Todo
      </h2>
      <el-row :gutter="20">
        <el-col :span="12" :offset="6">
          <div class="grid-content bg-purple">
            <input
              @keyup.enter="push_list"
              v-model="newTodo"
              placeholder="请输入任务"
            >
          </div>
        </el-col>
      </el-row>

      <br><br>
      <el-checkbox v-model="alldone"></el-checkbox>
      <el-lable>全选</el-lable>
      <el-button @click="changeV('all')">全部任务</el-button>
      <el-button @click="changeV('done')">已完成</el-button>
      <el-button @click="changeV('plan')">未完成</el-button>
    </section>
    <el-row :gutter="20">
      <el-col :span="12" :offset="6">
        <div class="grid-content bg-purple">
          <ul>
            <li
              v-for="i in lists"
              :class="{editing: i == editedTodo }"
            >
              <div class="viewtodo">
                <el-checkbox
                  v-model="i.m_checked"
                  class="m_checkbox">
                </el-checkbox>
                <span @dblclick="editTodo(i)">{{ i.title }}</span>
                <i class="removetodo el-icon-close" @click=removetodo(i)></i>
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
      </el-col>
    </el-row>
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


