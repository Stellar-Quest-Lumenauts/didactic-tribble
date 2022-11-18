<script lang="ts">
  import Card, { Content } from '@smui/card';
  import LayoutGrid, { Cell } from '@smui/layout-grid';
  import moment from 'moment';

  // SQ0501
  //const evtSource = new EventSource("https://horizon.stellar.org/accounts/GA4O6I5J2BPHHQT3RNCOFVXUSUVILNUAQU4UZAHDAMSYFENR3QII4QDP/payments?order=asc&include_failed=false&limit=200")
  //const start = moment("2022-11-11T02:00:00.000Z")
  
  //SQ0502
  //const evtSource = new EventSource("https://horizon.stellar.org/accounts/GCMOPDUBGJZ6IZSD4WRCGAC3VUFHQNRZEPHM2UB2V3QWVAJ7NDGHOOG7/payments?order=asc&include_failed=false&limit=200")
  //const start = moment("2022-11-14T14:00:00.000")
  
  //SQ503
  const evtSource = new EventSource("https://horizon.stellar.org/accounts/GCS4T4Z3E6WIRYGLW7BHKDZU2EAQBVB3PPE7UMG24OCXAFNLELIHKMJ3/payments?order=asc&include_failed=false&limit=200")
  const start = moment("2022-11-21T14:00:00.000Z")
  
  let winnerKey = undefined
  let winnerTime = ""

  let secondPlace = undefined
  let secondPlaceTime = undefined

  let thirdPlace = undefined
  let thirdPlaceTime = undefined

  let SomePlace = undefined
  let SomePlaceCounter = 4
  let someOtherTime = undefined

  evtSource.onmessage = (event) => {
    const data = JSON.parse(event.data)

    const hours = Math.abs(Math.floor(start.diff(moment(data["created_at"]), 'hours')))
    const minutes = Math.abs(Math.floor(start.diff(moment(data["created_at"]), 'minutes')) )
    const seconds = Math.abs(Math.floor(start.diff(moment(data["created_at"]), 'seconds') / 60))

    if (winnerKey == undefined) {
      winnerKey = data["to"]
      winnerTime = `${hours}:${minutes}:${seconds}`
    } else if (secondPlace == undefined) {
      secondPlace = data["to"]
      secondPlaceTime = `${hours}:${minutes}:${seconds}`
    } else if (thirdPlace == undefined) {
      thirdPlace = data["to"]
      thirdPlaceTime = `${hours}:${minutes}:${seconds}`
    } else {
      SomePlace = data["to"]
      someOtherTime = `${hours}:${minutes}:${seconds}`
      SomePlaceCounter++;
    }

    console.log(data)
  }

</script>
<h1>SQ0502: Leaderboard!</h1>
<main>

  <div class="leaders">
    <LayoutGrid>
      <Cell span={4}>
        <div class="card-container">
          <Card style="background-color: #212125 !important">
            <Content>
              <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/a/ad/Simple_silver_cup.svg/1280px-Simple_silver_cup.svg.png" height=100 width=100/>
              {#if secondPlace == undefined}
                <h6>Can it be you?</h6>
              {:else}
              <h6 id="winnerName">#2 {secondPlace}!</h6>
              <h4 id="time">Time: {secondPlaceTime}</h4>
              {/if}
            </Content>
          </Card>
        </div>
      </Cell>
      <Cell span={4}>
        <div class="card-container">
          <Card style="background-color: #212125 !important" >
            <Content>
              <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/0/07/Gold_Cup_icon.svg/1920px-Gold_Cup_icon.svg.png" height=200 width=200/>
              {#if winnerKey == undefined}
                <h1>Will you win the trophy?</h1>
              {:else }
                <h6 id="winnerName">#1 {winnerKey}!</h6>
                <h4 id="time">Time: {winnerTime}</h4>
              {/if}
            </Content>
          </Card>
        </div>
      </Cell>
      <Cell span={4}>
        <div class="card-container">
          <Card style="background-color: #212125 !important">
            <Content>
              <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/e/ed/Simple_bronze_cup.svg/1280px-Simple_bronze_cup.svg.png" height=100 width=100/>
              {#if thirdPlace == undefined}
                <h6>Or this trophy?</h6>
              {:else}
              <h6 id="winnerName">#3 {thirdPlace}!</h6>
              <h4 id="time">Time: {thirdPlaceTime}</h4>
              {/if}
            </Content>
          </Card>
        </div>
      </Cell>
  </LayoutGrid>
  </div>
  <Card style="background-color: #212125 !important; margin-top:20px;" >
    <Content>
      {#if SomePlace == undefined}
        <h4>But you'll always be shown here!</h4>
        
      {:else}
        <h4 style="text-align:left;">#{SomePlaceCounter}: {SomePlace}</h4>
      {/if}
    </Content>
  </Card>
</main>

<style>
  
</style>
