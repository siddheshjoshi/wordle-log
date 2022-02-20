<template>
    <div>
        <table class="table table-striped">
            <thead>
                <tr>
                <th scope="col">#</th>
                <th scope="col">GM</th>
                <th scope="col">AM</th>
                <th scope="col">AJ</th>
                <th scope="col">SJ</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="(log, index) in wordleScores" :key="index">
                <th scope="row">{{log.wordleDay}}</th>
                <td>{{log.playerScores.GM}}</td>
                <td>{{log.playerScores.AM}}</td>
                <td>{{log.playerScores.AJ}}</td>
                <td>{{log.playerScores.SJ}}</td>
                </tr>
            </tbody>
        </table>
    </div>
</template>

<script>

export default {
    props: ['wordleScores'],
    name: 'ScoresTable'
}
</script>
