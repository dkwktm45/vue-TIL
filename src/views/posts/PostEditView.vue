<template>
  <div>
    <h2>게시글 수정</h2>
    <hr class="my-4" />
    <form @submit.prevent = "edit">
      <div class="mb-3">
        <label for="title" class="form-label">제목</label>
        <input v-model="post.title" type="text" class="form-control" id="title" />
      </div>
      <div class="mb-3">
        <label for="content" class="form-label">내용</label>
        <textarea v-model="post.content" class="form-control" id="content" rows="3"></textarea>
      </div>
      <div class="pt-4">
        <button
          type="button"
          class="btn btn-outline-danger me-2"
          @click="goDetailPage"
        >
          취소
        </button>
        <button class="btn btn-primary">수정</button>
      </div>
    </form>
  </div>
</template>

<script setup>
import { useRouter } from 'vue-router';
import { useRoute } from 'vue-router';
import { getPostById, updatePost } from '@/api/posts';
import { ref } from '@vue/reactivity';

const router = useRouter();
const route = useRoute();
const id = route.params.id;

const goDetailPage = () =>
  router.push({
    name: 'PostDetail',
    params: { id },
  });

  const post = ref({
    title : null,
    content : null
  })
const fetchPost = async() =>{
  const { data } = await getPostById(id);
  setForm(data)
}

const setForm = ({title,content}) => {
  post.value.title = title;
  post.value.content = content;
}
fetchPost()
const edit = async() =>{
  try{
    await updatePost(id, {...post.value})
    router.push({name : "PostDetail",params : {id}})
  }catch(error){
    console.log(error)
  }
}
</script>

<style lang="scss" scoped></style>
