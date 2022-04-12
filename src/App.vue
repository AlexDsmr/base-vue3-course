<template>
    <div class="app">
    <h1>Страница с постами</h1>
        <my-button
        @click="showDialog"
        style="margin: 15px 0"
        >
        Создать пост
        </my-button>
        <my-dialog v-model:show="dialogvisible">
           <PostForm
                @create="createPost"
            />
        </my-dialog>
        <post-list 
        :posts="posts"
        @remove="removePost"
        v-if="!isPostsLoading"
        />
        <div v-else>Идёт загрузка...</div>
    </div>
</template>

<script>
import axios from "axios";
import PostForm from "@/components/PostForm";
import PostList from "@/components/Postlist";
export default {
    components:{
      PostList, PostForm
    },
    data(){
        return{
            posts: [],
            dialogvisible: false,
            isPostsLoading: false
        }
    },
    methods: {
        createPost(post) {
            this.posts.push(post);
            this.dialogvisible = false;
        },
        removePost(post) {
            this.posts = this.posts.filter(p => p.id !== post.id)
        },
        showDialog() {
            this.dialogvisible = true;
        },
        async fetchPosts() {
            try {
                this.isPostsLoading = true;
                const response = await axios.get('https://jsonplaceholder.typicode.com/posts?_limit=10');
                this.posts = response.data;
                this.isPostsLoading = false;
            } catch (e) {
                alert('Ошибка')
            } finally {
                this.isPostsLoading = false; 
            }
        }
    },
    mounted() { //Хук, доступный после внедрение компонента
        this.fetchPosts();
    }
}
</script>

<style>
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

.app {
    padding: 20px;
}


</style>