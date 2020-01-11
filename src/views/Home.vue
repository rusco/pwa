<template>
    <v-layout
        row
        wrap
    >
        <v-flex text-xs-center>
            <!-- header -->
            <h1 class="primary--text display-3 font-weight-medium my-3">AMKOR Checklist</h1>

            <v-card>
                <v-list class="pa-0">
                    <v-list-item>
                        <v-list-item-action>
                            <v-checkbox
                                :input-value="allChecked"
                                @change="toggleAll(!allChecked)"
                                color="primary"
                                v-if="todos.length > 0"
                            ></v-checkbox>
                            <v-icon
                                color="primary"
                                v-else
                            >mid-check</v-icon>
                        </v-list-item-action>
                        <v-text-field
                            class="pa-8"
                            :label="'New todo input'"
                            @keydown.enter="addTodo"
                            autofocus
                            autocomplete="off"
                            clearable
                            hide-details
                            maxlength="80"
                            counter
                            flat
                            solo
                            placeholder="What needs to be done?"
                            v-model="newTodo"
                        ></v-text-field>
                    </v-list-item>
                </v-list>
            </v-card>
            <!-- main -->
            <v-card
                class="mt-3"
                v-show="todos.length"
            >
                <v-progress-linear
                    class="my-0"
                    v-model="progressPercentage"
                />
                <v-card-actions
                    class="px-3"
                    v-show="todos.length"
                >
                    <span class="primary--text">
                        {{ remaining }} {{ remaining | pluralize('item') }} left
                    </span>
                    <v-spacer></v-spacer>
                    <v-btn-toggle
                        class="elevation-0"
                        mandatory
                        v-model="visibility"
                        v-show="todos.length"
                    >
                        <v-btn
                            :key="key"
                            :to="key"
                            :value="key"
                            class="mx-0"
                            color="primary"
                            text
                            v-for="(val, key) in filters"
                        >
                            {{ key | capitalize }}
                        </v-btn>
                    </v-btn-toggle>
                </v-card-actions>
                <v-list class="pa-0">
                    <template v-for="todo in filteredTodos">
                        <v-divider :key="`${todo.uid}-divider`"></v-divider>
                        <TodoItem
                            :key="todo.uid"
                            :todo="todo"
                        />
                    </template>
                </v-list>
            </v-card>

            <v-btn
                @click="clearCompleted"
                block
                class="mt-3"
                color="primary"
                rounded
                large
                v-show="todos.length > remaining"
            >
                Clear completed
            </v-btn>
            <v-fab-transition>
                <v-btn
                    @click="shareAll"
                    color="primary"
                    fab
                    large
                    dark
                    bottom
                    left
                    v-show="showShare"
                    class="v-btn--example mt-5"
                >
                    <v-icon>mdi-share-all</v-icon>
                </v-btn>
            </v-fab-transition>

            <!-- footer -->
            <footer-info></footer-info>
        </v-flex>
    </v-layout>
</template>

<script>
import { mapActions } from 'vuex'
import TodoItem from '@/components/TodoItem.vue'
import FooterInfo from '@/components/FooterInfo.vue'

const filters = {
    all: todos => todos,
    active: todos => todos.filter(todo => !todo.done),
    completed: todos => todos.filter(todo => todo.done)
}

export default {
    props: ['filter'],
    components: { TodoItem, FooterInfo },
    data() {
        return { newTodo: '', filters: filters, visibility: this.filter }
    },
    computed: {
        todos() {
            return this.$store.state.todos
        },
        allChecked() {
            return this.todos.every(todo => todo.done)
        },
        filteredTodos() {
            return filters[this.visibility](this.todos)
        },
        remaining() {
            return this.todos.filter(todo => !todo.done).length
        },
        progressPercentage() {
            const len = this.todos.length
            return ((len - this.remaining) * 100) / len
        },
        showShare() {
            return ("share" in window.navigator) && this.remaining > 0;
        }
    },
    methods: {
        ...mapActions(['toggleAll', 'clearCompleted']),
        addTodo() {
            const text = this.newTodo.trim()
            if (text) {
                this.$store.dispatch('addTodo', text)
            }
            this.newTodo = ''
        },
        shareAll() {
            let data = this.$store.state.todos.filter(todo => !todo.done).map(todo => todo.text).join(";");
            console.log(`Share: ${data}`)

            const shareOpts = {
                title: 'AmkorCheck',
                text: data
            };
            if ("share" in navigator)
                navigator.share(shareOpts);

        }
    },
    filters: {
        pluralize: (n, w) => n === 1 ? w : (w + 's'),
        capitalize: s => s.charAt(0).toUpperCase() + s.slice(1)
    }
}
</script>
<style type="text/css">
h1 {
    opacity: 0.3;
}
.v-text-field .v-input__slot {
    padding: 0 !important;
}
</style>
