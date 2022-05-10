<template>
    <div>

<h1>Страница с постами</h1>
    <my-input
        v-focus
        :model-value="searchQuery"
        @update:model-value="setSearchQuery"
        placeholder="Поиск..."
    />
        <div class="app__btns">
            <my-button
                @click="showDialog"
            >
            Создать пост
            </my-button>
            <my-select
                :model-value="selectedSort"
                @update:model-value="setSelectedSort"
                :options="sortOptions"
            />
        </div>
        <my-dialog v-model:show="dialogvisible">
           <PostForm
                @create="createPost"
            />
        </my-dialog>
        <post-list 
        :posts="sortedAndSearchedPosts"
        @remove="removePost"
        v-if="!isPostsLoading"
        />
        <div v-else>Идёт загрузка...</div>
        <div v-intersection="loadMorePosts" class="observer"></div>

<!--
        <div class="page__wrapper">
            <div
            v-for="pageNumber in totalPages"
            :key="pageNumber"
            class="page"
            :class="{
                'current-page': page === pageNumber
            }"
            @click="changePage(pageNumber)"
            >
            {{ pageNumber }}
            </div>
        </div>
-->
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
import {mapState, mapGetters, mapActions, mapMutations} from 'vuex'

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
        }
    },
    methods: {
        ...mapMutations({
            setPage: 'post/setPage',
            setSearchQuery: 'post/setSearchQuery',
            setSelectedSort: 'post/setSelectedSort',
        }),
        ...mapActions({
            loadMorePosts: 'post/loadMorePosts',
            fetchPosts: 'post/fetchPosts'
        }),
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
    },
    mounted() {
        this.fetchPosts();
    },
    computed: {
        ...mapState({
            posts: state => state.post.posts,
            isPostsLoading: state => state.post.isPostsLoading,
            selectedSort: state => state.post.selectedSort,
            searchQuery: state => state.post.searchQuery,
            page: state => state.post.page,
            limit: state => state.post.limit,
            totalPages: state => state.post.totalPages,
            sortOptions: state => state.post.sortOptions
        }),
        ...mapGetters ({
            sortedPosts: 'post/sertedPostes',
            sortedAndSearchedPosts: 'post/sortedAndSearchedPosts'
        })
    },
    watch: {
//        page() {
//            this.fetchPosts()
//        }
        
        //selectedSort(newValue) {
        //    this.posts.sort((post1, post2) => {
        //        if (newValue === 'id') {
        //            return post1[newValue]>post2[newValue]?1:-1
        //        } else {
        //            return post1[newValue]?.localeCompare(post2[newValue])  
        //        }
        //    })
        //},
        
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