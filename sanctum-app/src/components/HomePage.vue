<template>
  <nav class="navbar navbar-expand-lg navbar-light">
    <div class="container d-flex justify-content-between">
      <router-link to="/home" class="navbar-brand">ForumApp</router-link>
      <ul class="btn btn-primary">
        <router-link to="/logout" class="nav-link">Logout</router-link>
      </ul>
    </div>
  </nav>
  <div class="container mt-5">
    <h2 class="text-center mb-4">Posts</h2>
    <div class="row">
      <div class="col-md-8 mx-auto">
        <button class="btn btn-primary btn-block mb-3" @click="showPostForm = true">New Post</button>
        <div v-if="showPostForm" class="mb-3">
          <form @submit.prevent="createPost">
            <div class="form-group">
              <input type="text" class="form-control" v-model="newPost.title" placeholder="Title" required>
            </div>
            <div class="form-group">
              <textarea class="form-control" v-model="newPost.body" placeholder="Body" rows="5" required></textarea>
            </div>
            <div class="text-center">
              <button type="submit" class="btn btn-success">Submit</button>
              <button type="button" class="btn btn-secondary ml-2" @click="showPostForm = false">Cancel</button>
            </div>
          </form>
        </div>
        <div v-for="post in posts" :key="post.id" class="card mb-4">
          <div class="card-body d-flex flex-column">
            <div class="d-flex justify-content-between align-items-center mb-3">
              <h5 class="card-title border-bottom pb-2 mb-3">{{ post.title }}</h5>
              <div>
                <button class="btn btn-sm edit-btn" @click="openEditModal(post)">
                  <i class="fa-solid fa-pen"></i>
                </button>
                <button class="btn btn-sm delete-btn" @click="deletePost(post.id)">
                  <i class="fa-solid fa-trash"></i>
                </button>
              </div>
            </div>
            <p class="card-text">{{ post.body }}</p>
            <hr class="my-4">
            <div class="mt-auto">
              <h6>Comments</h6>
              <form @submit.prevent="createComment(post.id)">
                <div class="form-group">
                  <textarea class="form-control" v-model="newComment" placeholder="Add a comment" rows="3" required></textarea>
                </div>
                <div class="text-right">
                  <button type="submit" class="btn btn-primary">Comment</button>
                </div>
              </form>
              <div v-for="comment in post.comments" :key="comment.id" class="card mt-2">
                <div class="card-body">
                  <p class="card-text">{{ comment.body }}</p>
                  
                  <div class="button-container">
                      <button class="btn btn-sm" @click="openEditCommentModal(comment)">
                          <i class="fa-solid fa-pen"></i>
                      </button>
                      <button class="btn btn-sm" @click="deleteComment(comment)">
                          <i class="fas fa-trash-alt"></i>
                      </button>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Edit Post Modal -->
    <div v-if="showEditModal" class="modal fade show" style="display: block;" tabindex="-1" role="dialog">
      <div class="modal-dialog" role="document">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title">Edit Post</h5>
          </div>
          <div class="modal-body">
            <form @submit.prevent="updatePost">
              <div class="form-group">
                <input type="text" class="form-control" v-model="editPostData.title" placeholder="Title" required>
              </div>
              <div class="form-group">
                <textarea class="form-control" v-model="editPostData.body" placeholder="Body" required></textarea>
              </div>
              <div class="text-right">
                <button type="submit" class="btn btn-success mr-2">Update</button>
                <button type="button" class="btn btn-secondary" data-dismiss="modal" @click="showEditModal = false">Cancel</button>
              </div>
            </form>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      posts: [],
      newPost: {
        title: '',
        body: ''
      },
      editPostData: {
        id: null,
        title: '',
        body: ''
      },
      editCommentData: {
          id: null,
          body: ''
      },
      postId: 0,
      showEditCommentModal: false,
      newComment: '',
      showPostForm: false,
      showEditModal: false
    };
  },
  async mounted() {
    await this.fetchPosts();
  },
  methods: {
    async fetchPosts() {
      try {
        const response = await axios.get(`${this.$root.$data.apiUrl}/posts`, {
          headers: {
            Authorization: `Bearer ${localStorage.getItem('token')}`
          }
        });
        this.posts = response.data;
        for (const post of this.posts) {
          const commentsResponse = await axios.get(`${this.$root.$data.apiUrl}/posts/${post.id}/comments`, {
            headers: {
              Authorization: `Bearer ${localStorage.getItem('token')}`
            }
          });
          post.comments = commentsResponse.data;
        }
      } catch (error) {
        console.error("Error fetching posts:", error);
      }
    },
    async createPost() {
      try {
        const response = await axios.post(`${this.$root.$data.apiUrl}/posts`, this.newPost, {
          headers: {
            Authorization: `Bearer ${localStorage.getItem('token')}`
          }
        });
        this.posts.push(response.data);
        this.newPost.title = '';
        this.newPost.body = '';
        this.showPostForm = false;
      } catch (error) {
        console.error("Error creating post:", error);
      }
    },
    async updatePost() {
      //
    },
    async deletePost(postId) {
      //
    },
    async createComment(postId) {
      //
    },
    async updateComment() {
      //
    },
    async deleteComment(comment) {
      //
    }
  }
};
</script>

<style scoped>
.card {
  border-radius: 15px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.btn {
  border-radius: 6px;
  margin: 4px;
}

.navbar {
  background-color: #6DC5D1;
}

.navbar-brand {
  font-size: 24px;
  font-weight: bold;
}

.edit-btn, .delete-btn {
  background-color: transparent;
  border: none;
  color: #6c757d;
}

.edit-btn.i:hover, .delete-btn i:hover {
  color: #dc3545;
}

.custom-textarea {
  resize: none;
}

.card-title {
  border-bottom: 1px solid #dee2e6;
  padding-bottom: 5px;
}

.card-body {
  padding: 1.25rem;
}

.comment-card {
  border-radius: 10px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.button-container {
  position: absolute;
  bottom: 5px;
  right: 5px;
}

</style>
