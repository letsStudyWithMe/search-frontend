<template>
  <div class="index-page">
    <a-input-search
        v-model:value="searchText"
        placeholder="input search text"
        enter-button="Search"
        size="large"
        @search="onSearch"
    />
  </div>
  <div class="tabs">
    <a-tabs v-model:activeKey="activeKey">
      <a-tab-pane key="post" tab="文章">
        <PostList :post-list="postList"/>
      </a-tab-pane>
      <a-tab-pane key="picture" tab="图片">
        <PictureList :picture-list="pictureList"/>
      </a-tab-pane>
      <a-tab-pane key="user" tab="用户">
        <UserList :user-list="userList"/>
      </a-tab-pane>
    </a-tabs>
  </div>
</template>
<script setup lang="ts">
import {ref, watchEffect} from "vue";
import PostList from "@/components/PostList.vue";
import myAxios from "@/plugins/myAxios";
import UserList from "@/components/UserList.vue";
import PictureList from "@/components/PictureList.vue";
import {useRoute, useRouter} from "vue-router";

const route = useRoute();
const router = useRouter();
const activeKey = route.params.category;
const searchText = ref("");
const postList = ref([]);
const userList = ref([]);
const pictureList = ref([]);
const initSearchParams = {
  text: "",
  pageSize: 10,
  pageNum: 1,
};
const searchParams = ref(initSearchParams);
watchEffect(() => {
  searchParams.value = {
    ...initSearchParams,
    text: route.query.text,
  } as any;
});
const onSearch = (value: string) => {
  alert(value);
  router.push({
    query: searchParams.value,
  });
};
const onTabchange = (key: string) => {
  router.push({
    path: `/${key}`,
    query: searchParams.value,
  });
};
myAxios.post("/post/list/page/vo", {}).then((res: any) => {
  postList.value = res.records;
});
myAxios.post("/picture/list/page/vo", {}).then((res: any) => {
  pictureList.value = res.records;
});
myAxios.post("/user/list/page/vo", {}).then((res: any) => {
  userList.value = res.records;
});
</script>
<style scoped>
.tabs {
  margin-top: 20px;
}
</style>
