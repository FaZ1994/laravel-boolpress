<template>
    <section>
        <div class="card" style="width: 18rem" v-if="post">
            <img
                :src="`/storage/${post.image}`"
                class="card-img-top"
                :alt="`immagine` + post.title"
            />
            <div class="card-body">
                <h5 class="card-title">{{ post.title }}</h5>
                <p class="card-text">{{ post.content }}</p>
                <ul class="list-group list-group-horizontal" v-if="post.tags">
                    <li
                        class="list-group-item"
                        v-for="tag in post.tags"
                        :key="tag.id"
                    >
                        {{ tag.name }}
                    </li>
                </ul>

                <form @submit.prevent="addComment()"></form>
                <label for="username">Inserisci il nome</label>
                <input v-model="formData.username" type="text">
                <label for="content">Inserisci il commento</label>
                <input v-model="formData.content" type="text">
                <button type="submit" class="btn btn-primary">Invia</button>


                <div class="card p-3" v-if="post.comments.length > 0">
                    <div
                        class="d-flex justify-content-between align-items-center"
                    >
                        <div class="user d-flex flex-row align-items-center">
                            <!-- 
                            <img src="" width="30" class="user-img rounded-circle mr-2"> 
                        -->
                            <span
                                ><small class="font-weight-bold text-primary" v-for="comment in post.comments" :key="comment.id"
                                    >{{comment.username}}</small
                                >
                                <small class="font-weight-bold" v-for="comment in post.comments" :key="comment.id">
                                    {{comment.content}}
                                    </small
                                ></span
                            >
                        </div>

                        <small v-for="comment in post.comments" :key="comment.id">{{comment.created_at}}</small>
                    </div>

                    <!-- 
                    <div
                        class="action d-flex justify-content-between mt-2 align-items-center"
                    >
                        <div class="reply px-4">
                            <small>Remove</small>
                            <span class="dots"></span>
                            <small>Reply</small>
                            <span class="dots"></span>
                            <small>Translate</small>
                           
                        </div> 
                      
                        <div class="icons align-items-center">

                            <i class="fa fa-star text-warning"></i>
                            <i class="fa fa-check-circle-o check-icon"></i>
                            
                        </div>
                    </div>
                    -->
                </div>
            </div>
        </div>
    </section>
</template>
<script>
export default {
    name: "SinglePostComponent",
    data() {
        return {
            post: null,
            formData: {
                username : "",
                content : "" ,
                post_id : ""
                }
        };
    },
    methods:{
        addComment(){
            axios
            .post("/api/comments", this.formData)
            .then((response)=> {
                console.log(response);
                this.post.comments.push(response.data);

            })
            .catch((error)=> {
                console.log(error);
            });
        }
    },
    mounted() {
        const slug = this.$route.params.slug;
        axios
            .get(`/api/posts/${slug}`)
            .then((response) => {
                this.post = response.data;
                this.formData.post_id = this.post.id;
            })
            .catch((error) => {
                //handle error
                // console.log(error);
                this.$router.push({ name: "page-404" });
            });
    },

    computed:{
        formatDate() {
            return this.post.created_at(0, 19).replace("T", ",")
        }
    }
};
</script>
<style lang="scss"></style>
