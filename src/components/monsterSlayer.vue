<template>
    <div id="app">
        <section class="row">
            <div class="columns">
                <h1 class="text-center text-size">YOU</h1>
                <div class="healthbar">
                    <div 
                    class="healthbar text-center" 
                    style="background-color: green; margin:0; color:white;"
                    :style="{width: playerHealth+'%'}">
                        {{playerHealth}}
                    </div>
                </div>
            </div>
            <div class="columns">
                <h1 class="text-center text-size">MONSTER</h1>
                <div class="healthbar">
                    <div 
                    class="healthbar text-center" 
                    style="background-color:green; margin: 0; color: white;"
                    :style="{width: monsterHealth+'%'}">
                        {{monsterHealth}}
                    </div>
                </div>
            </div>
        </section>

        <section class="row button-center-new" v-if="!gameIsRunning">
            <button class="new-game" @click="startGame">START NEW GAME</button>
        </section>

        <section class="row button-center" v-else>
            <button class="attack" @click="attack">ATTACK</button>
            <button class="special-attack" @click="specialAttack">SPECIAL ATTACK</button>
            <button class="heal" @click="heal">HEAL</button>
            <button class="give-up" @click="giveUp">GIVE UP</button>
        </section>

        <section class="row">
            <div class="log">
                <ul>
                    <li 
                    v-for="(turn,index) in turns" :key="index"
                    :class="{'player-turn': turn.isPlayer, 'monster-turn': !turn.isPlayer}"
                    >
                        {{turn.text}}
                    </li>
                </ul>
            </div>
        </section>
    </div>    
</template>

<script>
export default {
    name: 'MonsterSlayer',
    data(){
        return{
            gameIsRunning: false,
            playerHealth: 100,
            monsterHealth: 100,
            turns: []
        }
    },
    methods:{
        startGame: function(){
            this.gameIsRunning = true;
            this.playerHealth = 100;
            this.monsterHealth = 100;
            this.turns = [];
        },
        attack: function(){
            //player attacks monster
            this.playerAttack();
            if(this.checkWin()){
                return;
            }
            //monster attacks player
           this.monsterAttack();
        },
        playerAttack: function(){
            var damage = this.calculateDamage(3,10);
            this.monsterHealth -= damage;
            this.turns.unshift({
                isPlayer: true,
                text: 'Player hits monster by '+ damage
            });
        },
        monsterAttack: function(){
            var damage = this.calculateDamage(5,13);
            this.playerHealth -= damage;
            this.checkWin();
            this.turns.unshift({
                isPlayer: false,
                text: 'Monster hits player by '+damage
            })
        },
        specialAttack: function(){
            //monster high damage
            var damage = this.calculateDamage(10,20);
            this.monsterHealth -= damage;
            this.turns.unshift({
                isPlayer: true,
                text: 'Player special attack monster by '+ damage
            });
            //player normal damage
            this.monsterAttack();
        },
        heal: function(){
            if(this.playerHealth >= 90){
                this.playerHealth = 100;
            }else{
                this.playerHealth += 10;
            }
            this.turns.unshift({
                isPlayer: true,
                text: 'Player heals by 10'
            })
            this.monsterAttack();
        },
        giveUp: function(){
            this.gameIsRunning = false;
        },
        checkWin: function(){
            if(this.playerHealth <= 0){
                if(confirm('you lose, play again?')){
                    this.startGame();
                }else{
                    this.gameIsRunning = false;
                }
                return true;
            }else if(this.monsterHealth <= 0){
                 if(confirm('you win, play again?')){
                    this.startGame();
                }else{
                    this.gameIsRunning = false;
                }
                return true;
            }
            return false;
        },
        calculateDamage(min,max){
            return Math.max(Math.floor((Math.random()*max)+1),min);
        }
    }
}
</script>

<style scoped>
    .row{
        margin: 40px;
        padding: 0;
    }
    .healthbar{
        height: 40px;
        width: 85%;
        background-color: #eee;
        color: white;
        transition: width 500ms;
        font-family: 'Gill Sans', 'Gill Sans MT', Calibri, 'Trebuchet MS', sans-serif;
        font-size: 20px;
    }
    .text-center{
        text-align: center;
    }
    .text-size{
        font-size: 32px;
        font-family: Cambria, Cochin, Georgia, Times, 'Times New Roman', serif;
    }
    .columns{
        display: inline-block;
        width: 50%;
    }
    .button-center{
        width: 1000px;
        margin: 0 auto;
    }
    .button-center-new{
        width: 280px;
        margin: 0 auto;
    }
    button{
        padding: 15px 10px;
        margin: 10px;
        width: 200px;
        font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
        font-weight: bold;
        border-radius: 5px;
        border: none;
        font-size: 18px;
    }
    .new-game{
        background-color: #2196F3;
        color: whitesmoke;
    }
    .new-game:hover{
        background-color: #BBDEFB;
        color: #2196F3;
    }
    .attack{
        background-color: #f44336;
        color: whitesmoke;
    }
    .attack:hover{
        background-color: #ffcdd2;
        color: #f44336;
    }
    .special-attack{
        background-color: #3F51B5;
        color: white;
    }
    .special-attack:hover{
        background-color: #C5CAE9;
        color: #3F51B5;
    }
    .heal{
        background-color: #8BC34A;
        color: white;
    }
    .heal:hover{
        background-color: #DCEDC8;
        color: #8BC34A;
    }
    .give-up{
        background-color: #607D8B;
        color: white;
    }
    .give-up:hover{
        background-color: #CFD8DC;
        color: #607D8B;
    }
    ul{
        list-style-type: none;
    }
    .log ul .player-turn{
        color: blue;
        background-color: #e4e8ff;
        padding: 5px;
        margin: 5px;
    }
    .log ul .monster-turn{
        color: red;
        background-color: #ffc0c1;
        padding: 5px;
        margin: 5px;
    }
</style>
