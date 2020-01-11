<template>
    <v-list-item
        class="todo-item"
        :class="{ 'editing': editing }"
    >

        <v-list-item-action>
            <v-checkbox
                :input-value="todo.done"
                @change="toggleTodo(todo)"
                color="primary"
                v-if="!editing"
            ></v-checkbox>
            <v-icon
                color="primary"
                v-else
            >mdi-pencil</v-icon>
        </v-list-item-action>
        <template v-if="!editing">
            <v-list-item-content
                :class="{ 'primary--text': todo.done }"
                @dblclick="editing = true"
            >
                {{ todo.text }}
            </v-list-item-content>
            <v-list-item-action>
                <v-btn
                    @click="removeTodo(todo)"
                    color="red lighten-3"
                    text
                    icon
                >
                    <v-icon>mdi-close</v-icon>
                </v-btn>
            </v-list-item-action>
        </template>
        <v-text-field
            :value="todo.text"
            @blur="doneEdit"
            @keyup.enter="doneEdit"
            @keyup.esc="cancelEdit"
            clearable
            color="primary"
            flat
            hide-details
            maxlength="1023"
            ref="input"
            solo
            large
            v-else
            v-focus="editing"
        ></v-text-field>
        <!--
        <v-btn
            class="mx-2"
            fab
            dark
            color="primary"
        >
            <v-icon dark>mdi-share</v-icon>
        </v-btn>
        -->

    </v-list-item>
</template>

<script>
import { mapActions } from 'vuex'

export default {
    props: ['todo'],
    data() {
        return {
            editing: false
        }
    },
    directives: {
        focus(el, { value }, { context }) {
            if (value) {
                context.$nextTick(() => {
                    context.$refs.input.focus()
                })
            }
        }
    },
    methods: {
        ...mapActions(['editTodo', 'removeTodo', 'toggleTodo']),
        doneEdit(e) {
            const value = e.target.value.trim()
            const { todo } = this
            if (!value) {
                this.removeTodo(todo)
            } else if (this.editing) {
                this.editTodo({ todo, value })
                this.editing = false
            }
        },
        cancelEdit() {
            this.editing = false
        }
    }
}
</script>

<style type="text/css">
.todo-item .v-list__item {
    height: auto;
    padding-top: 12px;
    padding-bottom: 12px;

    height: 40px;
}

.editing {
    height: auto;
}
</style>

