<template>
    <div>
    <h1>Страница с постами</h1>

    <my-input
        v-focus
        v-model="searchQuery"
        placeholder="Поиск..."
    />
        <div class="app__btns">
            <my-button

            >
            Создать пост
            </my-button>
            <my-select
                v-model="selectedSort"
                :options="sortOptions"
            />
        </div>
        <my-dialog v-model:show="dialogvisible">
           <PostForm
            />
        </my-dialog>
        <post-list 
        :posts="sortedAndSearchedPosts"
        v-if="!isPostsLoading"
        />
        <div v-else>Идёт загрузка...</div>

    </div>
</template>

<script>
import axios from "axios";
import PostForm from "@/components/PostForm";
import PostList from "@/components/Postlist";
import MyButton from "@/components/UI/MyButton";
import MySelect from "@/components/UI/MySelect";
import MyInput from "@/components/UI/MyInput";
import MyDialog from "@/components/UI/MyDialog";
import {ref} from 'vue';
import {usePosts} from "@/components/hooks/usePosts";
import useSortedPosts from "@/components/hooks/useSortedPosts";
import useSortedAndSearchedPosts from "@/components/hooks/useSortedAndSearchedPosts";

export default {
    components:{
        MyInput,
        MySelect,
        MyButton,
        MyDialog,
        PostList,
        PostForm
    },
    data(){
        return{
            dialogvisible: false,
            sortOptions: [
                {value: 'title', name: 'По названию'},
                {value: 'body', name: 'По содержимому'},
                {value: 'id', name: 'По ID'}
            ]
        }
    },
    setup(props){
        const {posts, totalPages, isPostsLoading} = usePosts(10);
        const {sortedPosts, selectedSort} = useSortedPosts(posts);
        const {searchQuery, sortedAndSearchedPosts} = useSortedAndSearchedPosts(sortedPosts)

        return {
            posts,
            totalPages,
            isPostsLoading,
            sortedPosts,
            selectedSort,
            searchQuery,
            sortedAndSearchedPosts
        }
    }
}
</script>

<style scoped>

.app__btns {
    display: flex;
    justify-content: space-between;
    margin: 15px 0;
}
.page__wrapper {
    display: flex;
    margin-top: 15px;
}

.page {
    border: 1px solid black;
    padding: 10px;
}
.current-page {
    border: 2px solid teal;
}

.observer {
    height: 30px;
    background: green;
}
</style>