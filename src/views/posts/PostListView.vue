<template>
  <div>
    <h2>게시글 목록</h2>
    <hr class="my-4" />
    <form @submit.prevent>
      <div class="row g-3">
        <div class="col">
          <input v-model="params.title_like" type="text" class="form-control"/>
        </div>
        <div class="col-3">
          <select class="form-select" v-model="params._limit">
            <option value="3">3개씩</option>
            <option value="6">6개씩</option>
            <option value="9">9개씩</option>
          </select>
        </div>
      </div>
    </form>
    <hr class="my-4" />
    <div class="row g-3">
      <div v-for="post in posts" :key="post.id" class="col-4">
        <PostItem
          :title="post.title"
          :content="post.content"
          :created-at="post.createdAt"
          @click="goPage(post.id)"
        >
        </PostItem>
      </div>
    </div>
    <nav class="mt-5" aria-label="Page navigation example">
      <ul class="pagination justify-content-center">
        <li class="page-item" :class="{disabled: !(params._page > 1)}">
          <a class="page-link" href="#" aria-label="Previous"
          @click.prevent="--params._page">
            <span aria-hidden="true">&laquo;</span>
          </a>
        </li>
        <li v-for="page in pageCount" :key="page" class="page-item" :class="{
          active: params._page === page
        }">
          <a class="page-link" href="#" @click.prevent="params._page = page">{{page}}</a>
        </li>
        <li class="page-item" :class="{
          disabled : !(params._page < pageCount)
        }">
          <a class="page-link" href="#" aria-label="Next"
          @click.prevent="++params._page">
            <span aria-hidden="true">&raquo;</span>
          </a>
        </li>
      </ul>
    </nav>
    <hr class="my-4" />
    <AppCard>
      <PostDetailView :id ="1"></PostDetailView>
    </AppCard>
  </div>
</template>

<script setup>
import PostItem from '@/components/posts/PostItem.vue';
import { getPost } from '@/api/posts';
import { computed, ref, watchEffect } from 'vue';
import { useRouter } from 'vue-router';
import PostDetailView from './PostDetailView.vue';
import AppCard from "../../components/posts/AppCard.vue"
const posts = ref([]);
const pageCount = computed(()=> Math.ceil(totalCount.value / params.value._limit))

// pagination
const params = ref({
  _sort : 'createdAt',
  _order: 'desc',
  _page : 1,
  _limit: 3,
  title_like : ''
})
const totalCount = ref(0)

const fetchPosts = async() => {
  const {data , headers} = await getPost(params.value);
  posts.value = data
  totalCount.value = headers['x-total-count']
  // 아래와 같이 데이터를 바로 넣어줄 수 있다.
  // ({data: posts.value} = await getPost());

};

// 반응형 값이 변경된다면 콜백함수로 watchEffect가
watchEffect(fetchPosts)

const router = useRouter();
const goPage = id => {
  router.push({
    name: 'PostDetail',
    params: {
      id,
    },
  });
};
fetchPosts();
</script>

<style lang="scss" scoped></style>
