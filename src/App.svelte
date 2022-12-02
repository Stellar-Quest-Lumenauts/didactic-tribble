<script lang="ts">
  import Card, { Content } from '@smui/card';
  import LayoutGrid, { Cell } from '@smui/layout-grid';
  import List, {
    Item,
    Graphic,
    Text,
    PrimaryText,
    SecondaryText,
  } from '@smui/list';
  import moment from 'moment';

  // SQ0501
  //const evtSource = new EventSource("https://horizon.stellar.org/accounts/GA4O6I5J2BPHHQT3RNCOFVXUSUVILNUAQU4UZAHDAMSYFENR3QII4QDP/payments?order=asc&include_failed=false&limit=200")
  //const start = moment("2022-11-11T02:00:00.000Z")
  
  //SQ0502
  //const evtSource = new EventSource("https://horizon.stellar.org/accounts/GCMOPDUBGJZ6IZSD4WRCGAC3VUFHQNRZEPHM2UB2V3QWVAJ7NDGHOOG7/payments?order=asc&include_failed=false&limit=200")
  //const start = moment("2022-11-14T14:00:00.000")
  
  //SQ503
  //const evtSource = new EventSource("https://horizon.stellar.org/accounts/GCS4T4Z3E6WIRYGLW7BHKDZU2EAQBVB3PPE7UMG24OCXAFNLELIHKMJ3/payments?order=asc&include_failed=false&limit=200")
  //const start = moment("2022-11-21T14:00:00.000Z")

  // Regressions that need to be fixed for the above lines of code ^


  // SQ0505
  //const evtSource = new EventSource("https://horizon.stellar.org/accounts/GBTN5KHEQC52MQLUYJDURUN2TSAPYOTSJPYXNXYRJ2GWQIM3MBB6EF6W/payments?order=asc&include_failed=false&limit=200")
  //const start = new Date("2022-11-28T14:00:00.000Z")

  // SQ0506
  const evtSource = new EventSource("https://horizon.stellar.org/accounts/GAIUESMWDEDXVODVZXXDPFC3FEUKRBUIRYDFGIAMH6EUWRKFQNL5LJB7/payments?order=asc&include_failed=false&limit=200")
  const start = moment("2022-12-02T02:00:00.000Z")
  
  let winnerKey = undefined
  let winnerTime = ""

  let secondPlace = undefined
  let secondPlaceTime = undefined

  let thirdPlace = undefined
  let thirdPlaceTime = undefined

  let SomePlace = undefined
  let SomePlaceCounter = 4
  let someOtherTime = undefined

  let places = []

  evtSource.onmessage = async (event) => {
    const data = JSON.parse(event.data)

    const response = await fetch(data["_links"]["transaction"]["href"]);
    const response_data = await response.json();
    console.log(response_data)

    if(response_data["memo_type"] == "none" || response_data["memo"].includes('-1')) { 
      return
    }

    let parts = response_data["memo"].split(":")
    let placement = parts[0]

    let delta = Math.abs(start - new Date(data["created_at"])) / 1000;
    // calculate (and subtract) whole days
    const days = Math.floor(delta / 86400);
    delta -= days * 86400;

    // calculate (and subtract) whole hours
    const hours = Math.floor(delta / 3600) % 24;
    delta -= hours * 3600;

    // calculate (and subtract) whole minutes
    const minutes = Math.floor(delta / 60) % 60;
    delta -= minutes * 60;

    // what's left is seconds
    var seconds = delta % 60;  // in theory the modulus is not required


    if (winnerKey == undefined && placement == 0) {
      winnerKey = data["to"]
      winnerTime = `${days}:${hours}:${minutes}:${seconds}`
    } else if (secondPlace == undefined && placement == 1) {
      secondPlace = data["to"]
      secondPlaceTime = `${days}:${hours}:${minutes}:${seconds}`
    } else if (thirdPlace == undefined && placement == 2) {
      thirdPlace = data["to"]
      thirdPlaceTime = `${days}:${hours}:${minutes}:${seconds}`
    } else {
      SomePlace = data["to"]
      someOtherTime = `${days}:${hours}:${minutes}:${seconds}`
      SomePlaceCounter++;
    }

    places.push({key: data["to"], time: `${days}:${hours}:${minutes}:${seconds}`})
    places = places
    //console.log(data)

  }

</script>
<h1>SQ0506: Leaderboard!</h1>
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
              {:else}
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
        <h4 style="text-align:left;">#{SomePlaceCounter}: {SomePlace} ({someOtherTime})</h4>
      {/if}
    </Content>
  </Card>

  <LayoutGrid>
    <Cell span={12} align="middle">
      <hr/>
      <h4>Full Scoreboard</h4>
      <List
      class="demo-list"
      twoLine
      avatarList
      singleSelection
    >
      {#each places as {key, time}, i}
        {#if key != undefined}
        <Item style="background-color: #212125 !important; margin-bottom:5px;">
          <Graphic
              style="background-image: url(https://place-hold.it/40x40?text={i}&fontsize=16);"
          />
          <Text>
            <PrimaryText id="winnerName">{key}</PrimaryText>
            <SecondaryText>{time}</SecondaryText>
          </Text>
        </Item>
        {/if}  
      {/each}
    </List>
  </Cell>
</LayoutGrid>
</main>

<style>
  
</style>
