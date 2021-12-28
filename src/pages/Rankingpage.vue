<template>
    <div>
        <h1 id="titel">
            Ranking
        </h1>           
        
            <table>
            <tr>
                    <th>ID</th>
                    <th>Artist Name</th>
                    <th>Title</th>
                    <th>Points</th>
                    <th>Spotify</th>
            </tr>
                
            <tr v-for="(song) in songs" :key="song.id">
                <td>{{song.id}}</td>
                <td>{{ song.artist.name}}</td>
                <td>{{ song.title }}</td>
                <td>{{song.points}}</td>
                <td><iframe :src="song.spotify" width="100%" height="80" frameBorder="0" allowfullscreen="" allow="autoplay; clipboard-write; encrypted-media; fullscreen; picture-in-picture"></iframe></td>
            </tr>
            </table> 
         
           
        <br>
        <button @click="goToPage('home')">
            Go to home
        </button>

    </div>
</template>

<script>
export default({
    name: "Rankingpage",
    mounted(){
        console.log("mounted");
        this.fetchSongs();
    },
    data(){
        return{
            songs: [],
        }
    },
    methods:{
        goToPage(page){
            this.$emit("change-page", page);
        },
        fetchSongs(){
                    // Get all songs

            const url = "http://webservies.be/eurosong/Songs";

            fetch(url)
                .then((response) => {
                    return response.json();
                })
                .then((songs) => {
                    this.fetchArtists(songs)
                });
        },
        fetchArtists(songs){
            // Get all artists

            const url = "http://webservies.be/eurosong/Artists";

            fetch(url)
                .then((response) => {
                    return response.json();
                })
                .then((artists) => {
                    songs.map((song) => {
                        const artist = artists.find((artist) => artist.id == song.artist);
                        song.artist = artist;
                        song.points = 0;

                        return song;
                    });

                    this.fetchVotes(songs);
                });

        },
        fetchVotes(songs){
            let teller = 0;
                songs.forEach((song, index) => {
                    let url = "http://webservies.be/eurosong/Songs/"+song.id+"/points";
                    fetch(url)
                    .then((response) => {
                        return response.json();
                    }).then((points) => {
                        songs[index].points = points;
                        teller++;
                        if(teller == songs.length){
                            songs.sort(function (a, b) {
                                return b.points - a.points;
                            });
                            
                        this.songs = songs;
                        }
                    });
                });
        }
    }
})
</script>