<script setup>
import TodoItem from '../components/TodoItem.vue'
import ModalTodos from '../components/ModalTodos.vue'
import ModalInfo from '../components/ModalInfo.vue'
import TodoModalDelete from '../components/TodoModalDelete.vue'
import { useRoute, useRouter } from 'vue-router'
import { ref, onMounted } from 'vue'

const route = useRoute()
const router = useRouter()
const todoTitle = ref('')
const selectedTodo = ref('')
const todos = ref([])
const editTitle = ref(false)
const showSort = ref(false)
const showModalTodos = ref(false)
const showModalDelete = ref(false)
const showInfo = ref(false)

function triggerInfo() {
    showInfo.value = true
    setTimeout(() => {
        showInfo.value = false
    }, 3000)
}
const getTitle = async () => {
    const id = route.params.id

    await fetch(`https://todo.api.devcode.gethired.id/activity-groups/${id}`)
        .then(response => response.json())
        .then(result => {
            if (result.title) {
                todoTitle.value = result.title
            }
        })
        .catch(error => console.log('error', error));
}

const getTodo = async () => {
    const id = route.params.id
    await fetch(`https://todo.api.devcode.gethired.id/todo-items?activity_group_id=${id}`)
        .then(response => response.json())
        .then(result => {
            if (result.data) {
                // console.log(result.data)
                todos.value = result.data
            }
        })
        .catch(error => console.log('error', error));
}

const saveItem = async (item) => {
    if (selectedTodo.value === '') {
        const body = JSON.stringify({
            "title": item.name,
            "activity_group_id": route.params.id
        })
        const requestOptions = {
            method: 'POST',
            body: body,
            redirect: 'follow',
            headers: {
                "Content-Type": "application/json",
            },
        };
        await fetch("https://todo.api.devcode.gethired.id/todo-items", requestOptions)
            .then(response => response.text())
            .then(result =>
                getTodo()
            )
            .catch(error => {
                if (error) {
                    console.log('error', error)
                }
            });
    }
    if (selectedTodo.value != '') {
        const body = JSON.stringify({
            "title": item.name,
            "priority": item.priority,
        })
        const requestOptions = {
            method: 'PATCH',
            body: body,
            redirect: 'follow',
            headers: {
                "Content-Type": "application/json",
            },
        };
        await fetch("https://todo.api.devcode.gethired.id/todo-items/" + item.id, requestOptions)
            .then(response => response.text())
            .then(result =>
                getTodo()
            )
            .catch(error => {
                if (error) {
                    console.log('error', error)
                }
            });
    }
    selectedTodo.value = ''
    showModalTodos.value = false
}

const checkItem = async (item) => {
    const body = JSON.stringify({
        "is_active": !item.is_active
    })
    const requestOptions = {
        method: 'PATCH',
        body: body,
        redirect: 'follow',
        headers: {
            "Content-Type": "application/json",
        },
    };
    await fetch("https://todo.api.devcode.gethired.id/todo-items/" + item.id, requestOptions)
        .then(response => response.text())
        .then(result =>
            getTodo()
        )
        .catch(error => {
            if (error) {
                console.log('error', error)
            }
        });
}
const editItem = (item) => {
    if (item !== '') {
        selectedTodo.value = item
    }
    showModalTodos.value = true
}
const deleteItem = (item) => {
    selectedTodo.value = item
    showModalDelete.value = true
}
const handleDelete = async () => {
    const id = selectedTodo.value.id
    const requestOptions = {
        method: 'DELETE',
        redirect: 'follow',
    };
    await fetch("https://todo.api.devcode.gethired.id/todo-items/" + id, requestOptions)
        .then(response => response.text())
        .then(result =>
            getTodo()
        )
        .catch(error => {
            if (error) {
                console.log('error', error)
            }
        });
    showModalDelete.value = false
    selectedTodo.value = ''
    triggerInfo()
}
const sort = (type) => {
    showSort.value = false
}

const changeTitle = async () => {
    console.log('clicked outside')
    const id = route.params.id
    const body = JSON.stringify({
        "title": todoTitle.value,
    })
    const requestOptions = {
        method: 'PATCH',
        body: body,
        redirect: 'follow',
        headers: {
            "Content-Type": "application/json",
        },
    };
    await fetch(`https://todo.api.devcode.gethired.id/activity-groups/${id}`, requestOptions)
        .then(response => response.text())
        .then(result =>
            getTitle()
        )
        .catch(error => {
            if (error) {
                console.log('error', error)
            }
        });

    editTitle.value = false
}
onMounted(() => {
    getTitle()
    getTodo()
})

</script>
<template>
    <div class="container min-h-screen">
        <div class="relative flex justify-between mt-8">
            <div class="flex">
                <span @click="router.back()" class="pr-4 my-auto" data-cy="todo-back-button">
                    <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5"
                        stroke="currentColor" class="w-6 h-6">
                        <path stroke-linecap="round" stroke-linejoin="round" d="M9 15L3 9m0 0l6-6M3 9h12a6 6 0 010 12h-3" />
                    </svg>
                </span>
                <div class="text-3xl text-slate-800 font-bold my-auto">
                    <h3 v-show="!editTitle" data-cy="todo-title">{{ todoTitle }}</h3>
                    <input v-if="editTitle" class="bg-transparent border-b-2 border-gray-800 focus:outline-none" type="text"
                        v-model="todoTitle" v-click-outside="changeTitle">
                </div>
                <span @click="editTitle = !editTitle" class="pl-3 text-slate-500 my-auto" data-cy="todo-title-edit-button">
                    <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5"
                        stroke="currentColor" class="w-6 h-6 mt-2">
                        <path stroke-linecap="round" stroke-linejoin="round"
                            d="M16.862 4.487l1.687-1.688a1.875 1.875 0 112.652 2.652L10.582 16.07a4.5 4.5 0 01-1.897 1.13L6 18l.8-2.685a4.5 4.5 0 011.13-1.897l8.932-8.931zm0 0L19.5 7.125M18 14v4.75A2.25 2.25 0 0115.75 21H5.25A2.25 2.25 0 013 18.75V8.25A2.25 2.25 0 015.25 6H10" />
                    </svg>
                </span>
            </div>
            <div class="flex">
                <div v-show="showSort" class="absolute top-0 right-[204px] bg-white border rounded shadow px-4 py-2">
                    <div @click="sort('desc')" class="flex gap-4 py-2" data-cy="sort-latest">
                        <span>
                            <svg class="w-6 h-6" viewBox="0 0 24 24" version="1.1" xmlns="http://www.w3.org/2000/svg"
                                xmlns:xlink="http://www.w3.org/1999/xlink" fill="currentColor">
                                <g id="SVGRepo_bgCarrier" stroke-width="0"></g>
                                <g id="SVGRepo_tracerCarrier" stroke-linecap="round" stroke-linejoin="round"></g>
                                <g id="SVGRepo_iconCarrier">
                                    <title>sort_descending_line</title>
                                    <g id="页面-1" stroke="none" stroke-width="1" fill="none" fill-rule="evenodd">
                                        <g id="Editor" transform="translate(-768.000000, -96.000000)" fill-rule="nonzero">
                                            <g id="sort_descending_line" transform="translate(768.000000, 96.000000)">
                                                <path
                                                    d="M24,0 L24,24 L0,24 L0,0 L24,0 Z M12.5934901,23.257841 L12.5819402,23.2595131 L12.5108777,23.2950439 L12.4918791,23.2987469 L12.4918791,23.2987469 L12.4767152,23.2950439 L12.4056548,23.2595131 C12.3958229,23.2563662 12.3870493,23.2590235 12.3821421,23.2649074 L12.3780323,23.275831 L12.360941,23.7031097 L12.3658947,23.7234994 L12.3769048,23.7357139 L12.4804777,23.8096931 L12.4953491,23.8136134 L12.4953491,23.8136134 L12.5071152,23.8096931 L12.6106902,23.7357139 L12.6232938,23.7196733 L12.6232938,23.7196733 L12.6266527,23.7031097 L12.609561,23.275831 C12.6075724,23.2657013 12.6010112,23.2592993 12.5934901,23.257841 L12.5934901,23.257841 Z M12.8583906,23.1452862 L12.8445485,23.1473072 L12.6598443,23.2396597 L12.6498822,23.2499052 L12.6498822,23.2499052 L12.6471943,23.2611114 L12.6650943,23.6906389 L12.6699349,23.7034178 L12.6699349,23.7034178 L12.678386,23.7104931 L12.8793402,23.8032389 C12.8914285,23.8068999 12.9022333,23.8029875 12.9078286,23.7952264 L12.9118235,23.7811639 L12.8776777,23.1665331 C12.8752882,23.1545897 12.8674102,23.1470016 12.8583906,23.1452862 L12.8583906,23.1452862 Z M12.1430473,23.1473072 C12.1332178,23.1423925 12.1221763,23.1452606 12.1156365,23.1525954 L12.1099173,23.1665331 L12.0757714,23.7811639 C12.0751323,23.7926639 12.0828099,23.8018602 12.0926481,23.8045676 L12.108256,23.8032389 L12.3092106,23.7104931 L12.3186497,23.7024347 L12.3186497,23.7024347 L12.3225043,23.6906389 L12.340401,23.2611114 L12.337245,23.2485176 L12.337245,23.2485176 L12.3277531,23.2396597 L12.1430473,23.1473072 Z"
                                                    id="MingCute" fill-rule="nonzero"> </path>
                                                <path
                                                    d="M18,4 C18.5523,4 19,4.44772 19,5 L19,17.414 L20.1213,16.2927 C20.5118,15.9022 21.145,15.9022 21.5355,16.2927 C21.9261,16.6832 21.9261,17.3164 21.5355,17.7069 L18.7071,20.5354 C18.3166,20.9259 17.6834,20.9259 17.2929,20.5354 L14.4645,17.7069 C14.0739,17.3164 14.0739,16.6832 14.4645,16.2927 C14.855,15.9022 15.4882,15.9022 15.8787,16.2927 L17,17.414 L17,5 C17,4.44772 17.4477,4 18,4 Z M11,18 C11.5523,18 12,18.4477 12,19 C12,19.51285 11.613973,19.9355092 11.1166239,19.9932725 L11,20 L4,20 C3.44772,20 3,19.5523 3,19 C3,18.48715 3.38604429,18.0644908 3.88337975,18.0067275 L4,18 L11,18 Z M13,11 C13.5523,11 14,11.4477 14,12 C14,12.51285 13.613973,12.9355092 13.1166239,12.9932725 L13,13 L4,13 C3.44772,13 3,12.5523 3,12 C3,11.48715 3.38604429,11.0644908 3.88337975,11.0067275 L4,11 L13,11 Z M13,4 C13.5523,4 14,4.44772 14,5 C14,5.55228 13.5523,6 13,6 L4,6 C3.44772,6 3,5.55228 3,5 C3,4.44772 3.44772,4 4,4 L13,4 Z"
                                                    id="形状" fill="#09244B"> </path>
                                            </g>
                                        </g>
                                    </g>
                                </g>
                            </svg>
                        </span>
                        <span>terbaru</span>
                    </div>
                    <div @click="sort('desc')" class="flex gap-4 py-2" data-cy="sort-oldest">
                        <span class="text-blue-500">
                            <svg fill="currentColor" class="w-6 h-6" viewBox="0 0 24 24" version="1.1"
                                xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
                                <g id="SVGRepo_bgCarrier" stroke-width="0"></g>
                                <g id="SVGRepo_tracerCarrier" stroke-linecap="round" stroke-linejoin="round"></g>
                                <g id="SVGRepo_iconCarrier">
                                    <title>sort_ascending_line</title>
                                    <g id="页面-1" stroke="none" stroke-width="1" fill="none" fill-rule="evenodd">
                                        <g id="Editor" transform="translate(-720.000000, -96.000000)" fill-rule="nonzero">
                                            <g id="sort_ascending_line" transform="translate(720.000000, 96.000000)">
                                                <path
                                                    d="M24,0 L24,24 L0,24 L0,0 L24,0 Z M12.5934901,23.257841 L12.5819402,23.2595131 L12.5108777,23.2950439 L12.4918791,23.2987469 L12.4918791,23.2987469 L12.4767152,23.2950439 L12.4056548,23.2595131 C12.3958229,23.2563662 12.3870493,23.2590235 12.3821421,23.2649074 L12.3780323,23.275831 L12.360941,23.7031097 L12.3658947,23.7234994 L12.3769048,23.7357139 L12.4804777,23.8096931 L12.4953491,23.8136134 L12.4953491,23.8136134 L12.5071152,23.8096931 L12.6106902,23.7357139 L12.6232938,23.7196733 L12.6232938,23.7196733 L12.6266527,23.7031097 L12.609561,23.275831 C12.6075724,23.2657013 12.6010112,23.2592993 12.5934901,23.257841 L12.5934901,23.257841 Z M12.8583906,23.1452862 L12.8445485,23.1473072 L12.6598443,23.2396597 L12.6498822,23.2499052 L12.6498822,23.2499052 L12.6471943,23.2611114 L12.6650943,23.6906389 L12.6699349,23.7034178 L12.6699349,23.7034178 L12.678386,23.7104931 L12.8793402,23.8032389 C12.8914285,23.8068999 12.9022333,23.8029875 12.9078286,23.7952264 L12.9118235,23.7811639 L12.8776777,23.1665331 C12.8752882,23.1545897 12.8674102,23.1470016 12.8583906,23.1452862 L12.8583906,23.1452862 Z M12.1430473,23.1473072 C12.1332178,23.1423925 12.1221763,23.1452606 12.1156365,23.1525954 L12.1099173,23.1665331 L12.0757714,23.7811639 C12.0751323,23.7926639 12.0828099,23.8018602 12.0926481,23.8045676 L12.108256,23.8032389 L12.3092106,23.7104931 L12.3186497,23.7024347 L12.3186497,23.7024347 L12.3225043,23.6906389 L12.340401,23.2611114 L12.337245,23.2485176 L12.337245,23.2485176 L12.3277531,23.2396597 L12.1430473,23.1473072 Z"
                                                    id="MingCute" fill-rule="nonzero"> </path>
                                                <path
                                                    d="M17.2929,3.46465 C17.6533615,3.10416077 18.2206207,3.07643083 18.6128973,3.38146018 L18.7071,3.46465 L21.5355,6.29307 C21.9261,6.6836 21.9261,7.31676 21.5355,7.70729 C21.1750385,8.06777 20.6077793,8.09549923 20.2155027,7.79047769 L20.1213,7.70729 L19,6.58597 L19,19 C19,19.5523 18.5523,20 18,20 C17.48715,20 17.0644908,19.613973 17.0067275,19.1166239 L17,19 L17,6.58597 L15.8787,7.70729 C15.4882,8.09781 14.855,8.09781 14.4645,7.70729 C14.1039462,7.34680077 14.0762112,6.77957355 14.3812953,6.38727848 L14.4645,6.29307 L17.2929,3.46465 Z M13,18 C13.5523,18 14,18.4477 14,19 C14,19.51285 13.613973,19.9355092 13.1166239,19.9932725 L13,20 L4,20 C3.44772,20 3,19.5523 3,19 C3,18.48715 3.38604429,18.0644908 3.88337975,18.0067275 L4,18 L13,18 Z M13,11 C13.5523,11 14,11.4477 14,12 C14,12.5523 13.5523,13 13,13 L4,13 C3.44772,13 3,12.5523 3,12 C3,11.4477 3.44772,11 4,11 L13,11 Z M11,4 C11.5523,4 12,4.44772 12,5 C12,5.55229 11.5523,6 11,6 L4,6 C3.44772,6 3,5.55229 3,5 C3,4.44772 3.44772,4 4,4 L11,4 Z"
                                                    id="形状" fill="#09244B"> </path>
                                            </g>
                                        </g>
                                    </g>
                                </g>
                            </svg>
                        </span>
                        <span>terlama</span>
                    </div>
                    <div @click="sort('desc')" class="flex gap-4 py-2" data-cy="sort-az">
                        <span class="text-blue-500">
                            <svg viewBox="0 0 24 24" version="1.1" xmlns="http://www.w3.org/2000/svg"
                                xmlns:xlink="http://www.w3.org/1999/xlink" fill="currentColor" class="w-6 h-6">
                                <g id="SVGRepo_bgCarrier" stroke-width="0"></g>
                                <g id="SVGRepo_tracerCarrier" stroke-linecap="round" stroke-linejoin="round"></g>
                                <g id="SVGRepo_iconCarrier">
                                    <title>A-Z_sort_ascending_letters_line</title>
                                    <g id="页面-1" stroke="none" stroke-width="1" fill="none" fill-rule="evenodd">
                                        <g id="Editor" transform="translate(-240.000000, -96.000000)">
                                            <g id="A-Z_sort_ascending_letters_line"
                                                transform="translate(240.000000, 96.000000)">
                                                <path
                                                    d="M24,0 L24,24 L0,24 L0,0 L24,0 Z M12.5934901,23.257841 L12.5819402,23.2595131 L12.5108777,23.2950439 L12.4918791,23.2987469 L12.4918791,23.2987469 L12.4767152,23.2950439 L12.4056548,23.2595131 C12.3958229,23.2563662 12.3870493,23.2590235 12.3821421,23.2649074 L12.3780323,23.275831 L12.360941,23.7031097 L12.3658947,23.7234994 L12.3769048,23.7357139 L12.4804777,23.8096931 L12.4953491,23.8136134 L12.4953491,23.8136134 L12.5071152,23.8096931 L12.6106902,23.7357139 L12.6232938,23.7196733 L12.6232938,23.7196733 L12.6266527,23.7031097 L12.609561,23.275831 C12.6075724,23.2657013 12.6010112,23.2592993 12.5934901,23.257841 L12.5934901,23.257841 Z M12.8583906,23.1452862 L12.8445485,23.1473072 L12.6598443,23.2396597 L12.6498822,23.2499052 L12.6498822,23.2499052 L12.6471943,23.2611114 L12.6650943,23.6906389 L12.6699349,23.7034178 L12.6699349,23.7034178 L12.678386,23.7104931 L12.8793402,23.8032389 C12.8914285,23.8068999 12.9022333,23.8029875 12.9078286,23.7952264 L12.9118235,23.7811639 L12.8776777,23.1665331 C12.8752882,23.1545897 12.8674102,23.1470016 12.8583906,23.1452862 L12.8583906,23.1452862 Z M12.1430473,23.1473072 C12.1332178,23.1423925 12.1221763,23.1452606 12.1156365,23.1525954 L12.1099173,23.1665331 L12.0757714,23.7811639 C12.0751323,23.7926639 12.0828099,23.8018602 12.0926481,23.8045676 L12.108256,23.8032389 L12.3092106,23.7104931 L12.3186497,23.7024347 L12.3186497,23.7024347 L12.3225043,23.6906389 L12.340401,23.2611114 L12.337245,23.2485176 L12.337245,23.2485176 L12.3277531,23.2396597 L12.1430473,23.1473072 Z"
                                                    id="MingCute" fill-rule="nonzero"> </path>
                                                <path
                                                    d="M10.7586,13 C11.6994,13 12.1893533,14.0920038 11.6139222,14.7923977 L11.5364,14.8778 L7.41424,19 L11,19 C11.5523,19 12,19.4477 12,20 C12,20.51285 11.613973,20.9355092 11.1166239,20.9932725 L11,21 L5.24145,21 C4.30065,21 3.81071515,19.9079962 4.3861123,19.2076023 L4.46363,19.1222 L8.58581,15 L5.00003,15 C4.44774,15 4.00003,14.5523 4.00003,14 C4.00003,13.48715 4.38606566,13.0644908 4.8834079,13.0067275 L5.00003,13 L10.7586,13 Z M17,4 C17.5523,4 18,4.44772 18,5 L18,17.414 L19.1213,16.2927 C19.5119,15.9022 20.145,15.9022 20.5356,16.2927 C20.9261,16.6832 20.9261,17.3164 20.5356,17.7069 L17.7071,20.5354 C17.3166,20.9259 16.6834,20.9259 16.2929,20.5354 L13.4645,17.7069 C13.074,17.3164 13.074,16.6832 13.4645,16.2927 C13.855,15.9022 14.4882,15.9022 14.8787,16.2927 L16,17.414 L16,5 C16,4.44772 16.4477,4 17,4 Z M8.00003,3 C8.674326,3 9.28067533,3.39562382 9.55608491,4.00153967 L9.60994,4.13453 L11.9418,10.6637 C12.1275,11.1838 11.8565,11.756 11.3364,11.9417 C10.85345,12.1142286 10.3254908,11.8927974 10.1038503,11.443863 L10.0583,11.3363 L9.58102,10 L6.41903,10 L5.94177,11.3363 C5.75602,11.8564 5.1838,12.1275 4.66369,11.9417 C4.18073071,11.7692643 3.91252214,11.2635709 4.02540056,10.7757876 L4.05829,10.6637 L6.39012,4.13453 C6.63311,3.45416 7.27757,3 8.00003,3 Z M8.00003,5.57321 L7.13332,8 L8.86674,8 L8.00003,5.57321 Z"
                                                    id="形状" fill="#09244B"> </path>
                                            </g>
                                        </g>
                                    </g>
                                </g>
                            </svg>
                        </span>
                        <span>A-Z</span>
                    </div>
                    <div @click="sort('desc')" class="flex gap-4 py-2" data-cy="sort-za">
                        <span class="text-blue-500">
                            <svg class="w-6 h-6" viewBox="0 0 24 24" version="1.1" xmlns="http://www.w3.org/2000/svg"
                                xmlns:xlink="http://www.w3.org/1999/xlink" fill="currentColor">
                                <g id="SVGRepo_bgCarrier" stroke-width="0"></g>
                                <g id="SVGRepo_tracerCarrier" stroke-linecap="round" stroke-linejoin="round"></g>
                                <g id="SVGRepo_iconCarrier">
                                    <title>Z-A_sort_descending_letters_line</title>
                                    <g id="页面-1" stroke="none" stroke-width="1" fill="none" fill-rule="evenodd">
                                        <g id="Editor" transform="translate(-816.000000, -96.000000)" fill-rule="nonzero">
                                            <g id="Z-A_sort_descending_letters_line"
                                                transform="translate(816.000000, 96.000000)">
                                                <path
                                                    d="M24,0 L24,24 L0,24 L0,0 L24,0 Z M12.5934901,23.257841 L12.5819402,23.2595131 L12.5108777,23.2950439 L12.4918791,23.2987469 L12.4918791,23.2987469 L12.4767152,23.2950439 L12.4056548,23.2595131 C12.3958229,23.2563662 12.3870493,23.2590235 12.3821421,23.2649074 L12.3780323,23.275831 L12.360941,23.7031097 L12.3658947,23.7234994 L12.3769048,23.7357139 L12.4804777,23.8096931 L12.4953491,23.8136134 L12.4953491,23.8136134 L12.5071152,23.8096931 L12.6106902,23.7357139 L12.6232938,23.7196733 L12.6232938,23.7196733 L12.6266527,23.7031097 L12.609561,23.275831 C12.6075724,23.2657013 12.6010112,23.2592993 12.5934901,23.257841 L12.5934901,23.257841 Z M12.8583906,23.1452862 L12.8445485,23.1473072 L12.6598443,23.2396597 L12.6498822,23.2499052 L12.6498822,23.2499052 L12.6471943,23.2611114 L12.6650943,23.6906389 L12.6699349,23.7034178 L12.6699349,23.7034178 L12.678386,23.7104931 L12.8793402,23.8032389 C12.8914285,23.8068999 12.9022333,23.8029875 12.9078286,23.7952264 L12.9118235,23.7811639 L12.8776777,23.1665331 C12.8752882,23.1545897 12.8674102,23.1470016 12.8583906,23.1452862 L12.8583906,23.1452862 Z M12.1430473,23.1473072 C12.1332178,23.1423925 12.1221763,23.1452606 12.1156365,23.1525954 L12.1099173,23.1665331 L12.0757714,23.7811639 C12.0751323,23.7926639 12.0828099,23.8018602 12.0926481,23.8045676 L12.108256,23.8032389 L12.3092106,23.7104931 L12.3186497,23.7024347 L12.3186497,23.7024347 L12.3225043,23.6906389 L12.340401,23.2611114 L12.337245,23.2485176 L12.337245,23.2485176 L12.3277531,23.2396597 L12.1430473,23.1473072 Z"
                                                    id="MingCute" fill-rule="nonzero"> </path>
                                                <path
                                                    d="M8.00003,12 C8.674326,12 9.28067533,12.3956587 9.55608491,13.0015223 L9.60994,13.1345 L11.9418,19.6637 C12.1275,20.1838 11.8565,20.756 11.3364,20.9417 C10.85345,21.1142286 10.3254908,20.8927974 10.1038503,20.443863 L10.0583,20.3363 L9.58102,19 L6.41903,19 L5.94177,20.3363 C5.75602,20.8564 5.1838,21.1275 4.66369,20.9417 C4.18073071,20.7692643 3.91252214,20.2635709 4.02540056,19.7757876 L4.05829,19.6637 L6.39012,13.1345 C6.63311,12.4542 7.27757,12 8.00003,12 Z M17,4 C17.5523,4 18,4.44772 18,5 L18,17.414 L19.1213,16.2927 C19.5119,15.9022 20.145,15.9022 20.5356,16.2927 C20.9261,16.6832 20.9261,17.3164 20.5356,17.7069 L17.7071,20.5354 C17.3166,20.9259 16.6834,20.9259 16.2929,20.5354 L13.4645,17.7069 C13.074,17.3164 13.074,16.6832 13.4645,16.2927 C13.855,15.9022 14.4882,15.9022 14.8787,16.2927 L16,17.414 L16,5 C16,4.44772 16.4477,4 17,4 Z M8.00003,14.5732 L7.13332,17 L8.86674,17 L8.00003,14.5732 Z M10.7586,3 C11.6994,3 12.1893533,4.09196698 11.6139222,4.79241099 L11.5364,4.87782 L7.41424,9 L11,9 C11.5523,9 12,9.44772 12,10 C12,10.51285 11.613973,10.9355092 11.1166239,10.9932725 L11,11 L5.24145,11 C4.30065,11 3.81071515,9.90803302 4.3861123,9.20758901 L4.46363,9.12218 L8.58581,5 L5.00003,5 C4.44774,5 4.00003,4.55228 4.00003,4 C4.00003,3.48716857 4.38606566,3.06449347 4.8834079,3.0067278 L5.00003,3 L10.7586,3 Z"
                                                    id="形状" fill="#09244B"> </path>
                                            </g>
                                        </g>
                                    </g>
                                </g>
                            </svg>
                        </span>
                        <span>Z-A</span>
                    </div>
                    <div @click="sort('desc')" class="flex gap-4 py-2" data-cy="sort-unfinished">
                        <span>
                            <svg viewBox="0 0 24 24" version="1.1" xmlns="http://www.w3.org/2000/svg"
                                xmlns:xlink="http://www.w3.org/1999/xlink" fill="currentColor" class="w-6 h-6">
                                <g id="SVGRepo_bgCarrier" stroke-width="0"></g>
                                <g id="SVGRepo_tracerCarrier" stroke-linecap="round" stroke-linejoin="round"></g>
                                <g id="SVGRepo_iconCarrier">
                                    <title>history_line</title>
                                    <g id="页面-1" stroke="none" stroke-width="1" fill="none" fill-rule="evenodd">
                                        <g id="System" transform="translate(-624.000000, 0.000000)" fill-rule="nonzero">
                                            <g id="history_line" transform="translate(624.000000, 0.000000)">
                                                <path
                                                    d="M24,0 L24,24 L0,24 L0,0 L24,0 Z M12.5934901,23.257841 L12.5819402,23.2595131 L12.5108777,23.2950439 L12.4918791,23.2987469 L12.4918791,23.2987469 L12.4767152,23.2950439 L12.4056548,23.2595131 C12.3958229,23.2563662 12.3870493,23.2590235 12.3821421,23.2649074 L12.3780323,23.275831 L12.360941,23.7031097 L12.3658947,23.7234994 L12.3769048,23.7357139 L12.4804777,23.8096931 L12.4953491,23.8136134 L12.4953491,23.8136134 L12.5071152,23.8096931 L12.6106902,23.7357139 L12.6232938,23.7196733 L12.6232938,23.7196733 L12.6266527,23.7031097 L12.609561,23.275831 C12.6075724,23.2657013 12.6010112,23.2592993 12.5934901,23.257841 L12.5934901,23.257841 Z M12.8583906,23.1452862 L12.8445485,23.1473072 L12.6598443,23.2396597 L12.6498822,23.2499052 L12.6498822,23.2499052 L12.6471943,23.2611114 L12.6650943,23.6906389 L12.6699349,23.7034178 L12.6699349,23.7034178 L12.678386,23.7104931 L12.8793402,23.8032389 C12.8914285,23.8068999 12.9022333,23.8029875 12.9078286,23.7952264 L12.9118235,23.7811639 L12.8776777,23.1665331 C12.8752882,23.1545897 12.8674102,23.1470016 12.8583906,23.1452862 L12.8583906,23.1452862 Z M12.1430473,23.1473072 C12.1332178,23.1423925 12.1221763,23.1452606 12.1156365,23.1525954 L12.1099173,23.1665331 L12.0757714,23.7811639 C12.0751323,23.7926639 12.0828099,23.8018602 12.0926481,23.8045676 L12.108256,23.8032389 L12.3092106,23.7104931 L12.3186497,23.7024347 L12.3186497,23.7024347 L12.3225043,23.6906389 L12.340401,23.2611114 L12.337245,23.2485176 L12.337245,23.2485176 L12.3277531,23.2396597 L12.1430473,23.1473072 Z"
                                                    id="MingCute" fill-rule="nonzero"> </path>
                                                <path
                                                    d="M3.33995,7.0002 C6.10138,2.21728 12.2173,0.578527 17.0002,3.33995 C20.5899,5.41249 22.4067,9.37327 21.9254,13.2397 C21.8724,13.6662 21.7913,14.0916 21.6816,14.5132 C21.465219,15.3451048 20.4599039,15.5867147 19.8741632,15.0441848 L19.7896,14.9569 L17.5383,12.3696 C16.8534643,11.5826464 17.5459179,10.3982763 18.5348531,10.5596437 L18.6459,10.5832 L19.9272,10.9176 C19.6024,8.55192 18.2265,6.35734 16.0002,5.072 C12.1739,2.86286 7.28114,4.17386 5.072,8.0002 C2.86286,11.8265 4.17386,16.7193 8.0002,18.9284 C11.2963,20.8314 15.3861,20.1225 17.8674,17.4415 C18.2425,17.0362 18.8752,17.0117 19.2805,17.3868 C19.6859,17.762 19.7103,18.3947 19.3352,18.8 C16.2333,22.1515 11.1228,23.0406 7.0002,20.6605 C2.21728,17.899 0.578528,11.7831 3.33995,7.0002 Z M12,5.99999 C12.51285,5.99999 12.9355092,6.38602566 12.9932725,6.8833679 L13,6.99999 L13,11.5858 L15.7071,14.2929 C16.0976,14.6834 16.0976,15.3166 15.7071,15.7071 C15.3466385,16.0675615 14.7793793,16.0952893 14.3871027,15.7902834 L14.2929,15.7071 L11.2929,12.7071 C11.1365667,12.55085 11.0373861,12.3481417 11.0086685,12.1314345 L11,11.99999 L11,6.99999 C11,6.4477 11.4477,5.99999 12,5.99999 Z"
                                                    id="形状" fill="#09244B"> </path>
                                            </g>
                                        </g>
                                    </g>
                                </g>
                            </svg>
                        </span>
                        <span>belum selesai</span>
                    </div>
                </div>
                <div @click="showSort = !showSort" class="p-3 border rounded-full mx-3" data-cy="todo-sort-button">
                    <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor" class="w-5 h-5">
                        <path fill-rule="evenodd"
                            d="M2.24 6.8a.75.75 0 001.06-.04l1.95-2.1v8.59a.75.75 0 001.5 0V4.66l1.95 2.1a.75.75 0 101.1-1.02l-3.25-3.5a.75.75 0 00-1.1 0L2.2 5.74a.75.75 0 00.04 1.06zm8 6.4a.75.75 0 00-.04 1.06l3.25 3.5a.75.75 0 001.1 0l3.25-3.5a.75.75 0 10-1.1-1.02l-1.95 2.1V6.75a.75.75 0 00-1.5 0v8.59l-1.95-2.1a.75.75 0 00-1.06-.04z"
                            clip-rule="evenodd" />
                    </svg>
                </div>

                <div @click="editItem('')" class="flex px-5 py-2 font-semibold bg-blue-500 text-lg text-white rounded-full"
                    data-cy="todo-add-button">
                    <span class="my-auto">
                        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5"
                            stroke="currentColor" class="w-6 h-6">
                            <path stroke-linecap="round" stroke-linejoin="round" d="M12 6v12m6-6H6" />
                        </svg>
                    </span>
                    <span class="px-2">Tambah</span>
                </div>
            </div>
        </div>

        <div v-if="todos.length > 0" class="w-full mt-6" data-cy="todo-list">
            <!-- todo list -->
            <TodoItem v-for="todo, index in todos" @check="checkItem(todo)" @edit="editItem(todo)"
                @delete="deleteItem(todo)" :data="todo" :data-cy="'todo-item-' + index" :key="index" />
        </div>
        <div v-else class="mt-6 w-full h-60 bg-gray-200 border border-b-2 border-b-gray-50 rounded-xl shadow-inner min-h-32"
            data-cy="todo-empty-state">
            <div class="w-full p-8 text-center text-gray-500">
                <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5"
                    stroke="currentColor" class="w-10 h-10 mx-auto">
                    <path stroke-linecap="round" stroke-linejoin="round"
                        d="M18.364 18.364A9 9 0 005.636 5.636m12.728 12.728A9 9 0 015.636 5.636m12.728 12.728L5.636 5.636" />
                </svg>

                <h3 class="mt-4 text-xl font-semibold">List Todo Masih Kosong.</h3>
                <p class="mt-4 font-bold text-blue-500">
                    Tambah Todo
                </p>
            </div>
        </div>

    </div>
    <ModalTodos v-if="showModalTodos" @save="saveItem" @cancel="showModalTodos = false, selectedTodo = ''"
        :edit-item="selectedTodo" />

    <TodoModalDelete v-if="showModalDelete" @confirm="handleDelete(selectedTodo)"
        @cancel="showModalDelete = false, selectedTodo = ''" :todo="selectedTodo" />

    <ModalInfo v-if="showInfo" />
</template>