<template>
    <div id="movie-list">
        <movie-item v-for="movie in filteredMovies" class="movie"
                    v-bind:movie="movie.movie"
                    v-bind:sessions="movie.sessions"
                    v-bind:day="day"
                    v-bind:time="time"> {{ movie.movie.Title }}</movie-item>
    </div>
</template>

<script>
    import genres from '../util/genres';
    import times from '../util/times';
    import MovieItem from './MovieItem.vue';
    export default{

        props:[
            'genre', 'time', 'movies', 'day'
        ],
        components:{
            MovieItem
        },
        methods:{
            moviePassesGenreFilter(movie){
                if(!this.genre.length){
                    return true;
                }else{
                    return this.genre.find(genre => movie.genre === genre);
                }
            },
            sessionPassesTimeFilter(session){
                if(!this.day.isSame(this.$moment(session.time), 'day')){
                    return false;
                }else if(this.time.length === 0 || this.time.length === 2){
                    return true
                }else if (this.time[0] === times.AFTER_6PM){
                    return this.$moment(session.time).hour() >= 18;
                }else{
                    return this.$moment(session.time).hour() < 18;
                }

            }
        },
        computed: {
            filteredMovies(){
                return this.movies
                        .filter(this.moviePassesGenreFilter)
                        .filter(movie => movie.sessions.find(this.sessionPassesTimeFilter));
            }
        },


    }
</script>