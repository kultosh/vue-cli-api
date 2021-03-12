<template>
    <div>
        <h2>Add Article</h2>
        <form @submit.prevent="addArticle">
            <div class="form-group mb-3">
                <input type="text" class="form-control" placeholder="Title" v-model="article.title">
            </div>
            <div class="form-group">
                <textarea cols="100" rows="10" v-model="article.body"></textarea>
            </div>
            <button class="btn btn-light btn-block">Submit</button>
        </form>
        <nav aria-label="Page navigation example">
            <ul class="pagination">
                <li v-bind:class="[{'page-item': true}, {disabled: !pagination.prev_page_url}]"><a class="page-link" href="#" @click='fetchArticles(pagination.prev_page_url)'>Previous</a></li>
                <li class="page-item disabled"><a class="page-link text-dark" href="#">Page {{pagination.current_page}} of {{pagination.last_page}}</a></li>
                <li v-bind:class="[{'page-item': true}, {disabled: !pagination.next_page_url}]"><a class="page-link" href="#" @click='fetchArticles(pagination.next_page_url)'>Next</a></li>
            </ul>
        </nav>
        <div class="card card-body" v-for="article in articles" v-bind:key="article.id">
            <h3>{{article.title}}</h3>
            <p>{{article.body}}</p>
            <hr>
            <button class="btn btn-primary" @click="editArticle(article)">Edit</button>
            <button class="btn btn-danger" @click="deleteArticle(article.id)">Delete</button>
        </div>
    </div>
</template>

<script>
const axios = require("axios");
export default {
    data() {
        return {
            articles: [],
            article: {
                id: '',
                title: '',
                body: ''
            },
            article_id: '',
            pagination: {},
            edit: false,
        }
    },

    created() {
        this.fetchArticles();
    },

    methods: {
     fetchArticles(page_url) {
        page_url = page_url || 'http://127.0.0.1:8000/api/articles'
        let vm = this;
        axios.get(page_url)
            .then(res => {
                this.articles = res.data.data;
                vm.makePagination(res.data.meta, res.data.links);
            })
            .catch(err => console.log(err));
        },

        makePagination(meta, links) {
            let pagination = {
                current_page: meta.current_page,
                last_page: meta.last_page,
                next_page_url: links.next,
                prev_page_url: links.prev
            }
            this.pagination = pagination;
        },

        deleteArticle(id) {
            axios.delete(`http://127.0.0.1:8000/api/article/${id}`)
            .then(res => {
                alert('Article Removed!');
                this.fetchArticles();
            })
            .catch(err => console.log(err))
        },

        addArticle() {
            let data= this.article
            if(this.edit === false)
            {
                axios.post('http://127.0.0.1:8000/api/article',data,
                   { 
                       Headers: {
                            'Content-Type': 'application/json'
                        },
                    }
                )
                .then(res => {
		        console.log(res.data)
                    this.article.title = ''
                    this.article.body = ''
                    this.fetchArticles()
                })
                .catch(err => console.log(err));
            }
            else
            {
		        // let data = this.article
                axios.put('http://127.0.0.1:8000/api/article', data,{
                    Headers: {
                        'Content-Type': 'application/json'
                    },
                })
                .then(res => {
                    console.log(res)
                    this.article.title = ''
                    this.article.body = ''
                    this.fetchArticles()
                })
                .catch(err => console.log(err));
            }
        },

        editArticle(article) {
            this.edit = true
            this.article.id = article.id,
	        this.article.article_id = article.id,
            this.article.title = article.title,
            this.article.body = article.body
        }
    },
}
</script>
