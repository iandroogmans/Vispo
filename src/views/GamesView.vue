<script setup>
import { ref } from 'vue'
import DataView from 'primevue/dataview'
import ProgressSpinner from 'primevue/progressspinner'

import { supabase } from '../supabase'

const games = ref(0)

async function getGames() {
  const { data, error } = await supabase
    .from('matches')
    .select(
      `
        match_datetime,
        uuid,
        home_team: teams!home_team_id(uuid, name),
        away_team: teams!away_team_id(uuid, name),
        score_home_team,
        score_away_team
                    `,
    )
    .order('match_datetime', { ascending: false })
  games.value = data
}

getGames()
</script>

<template>
  <div class="container mx-auto pt-3">
    <ul class="">
      <li
        v-for="game in games"
        class="flex items-start space-x-6 p-6 mb-3 rounded-lg border border-slate-200 bg-white shadow-sm text-slate-900"
      >
        <div class="grow basis-0 text-right font-semibold truncate">
          {{ game.home_team.name }}
        </div>
        <div class="basis-32 text-center">
          {{ game.score_home_team + ' - ' + game.score_away_team }}
        </div>
        <div class="grow basis-0 text-left font-semibold truncate">
          {{ game.away_team.name }}
        </div>
      </li>
    </ul>
  </div>
</template>

<style>
@media (min-width: 1024px) {
  .about {
    min-height: 100vh;
    display: flex;
    align-items: center;
  }
}
</style>
