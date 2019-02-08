<template>
<div class="col-xs-12 text-center cards-container">
 <div class="col-xs-8">
  <h3 class="text-left" style="margin-bottom:0px;"><strong>Memory Game</strong></h3>
  </div>
  <div class="col-xs-4">
<h4 style="margin-top:22px;margin-bottom:0px;">Level {{ difficulty }}</h4>
</div>
<div class="col-xs-12 text-left details">
	
	<hr>

		<div class="btn-group">
  <button type="button" class="btn btn-sm btn-primary dropdown-toggle" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
    Menu <span class="caret"></span>
  </button>
  <ul class="dropdown-menu">
    <li><a href="#" @click.prevent="reshuffle('reset')">Restart</a></li>
  </ul>
</div>

	<span class="label label-default"><strong>Points:</strong> {{ points }}</span> 
	<span class="label label-default"><strong>Matches:</strong> {{ matches }}</span> 
	<span class="label label-default"><strong>Attempts:</strong> {{ attempts }}</span>
	
	<hr>
	
</div>
<div class="col-md-12 col-sm-12 col-xs-12">
<p v-for="(cards,index) in allCards" class="card text-center" :class="{cardplay:cards.action}" @click="flip(cards,index)"><i class="fa fa-3x icon"  :class="cards.icon"></i></p>
</div>
<div v-if="win" class="faded"></div>
<div  v-if="win" class="win-box">
<div class="col-xs-12 congrats" v-if="win">
<h1 style="margin-top:0px;"><strong>Congrats You won!</strong></h1>
<h3>Continue to the next level</h3>
<p><i class="fa fa-trophy fa-3x"></i></p>
<button class="btn btn-success btn-lg btn-new" @click="reshuffle('levelup')">Continue</button>
</div>
</div>
</div>
</template>

<script>

export default{

	data:
		function(){
			return {
				
				cardsInfo:[
				{id:1, icon:"fa-eye", action:false},
				{id:2, icon:"fa-balance-scale", action:false},
				{id:3, icon:"fa-bath", action:false},
				{id:4, icon:"fa-bug", action:false},
				{id:5, icon:"fa-camera", action:false},
				{id:6, icon:"fa-coffee", action:false},
				{id:7, icon:"fa-cubes", action:false},
				{id:8, icon:"fa-eraser", action:false},
				{id:9, icon:"fa-flash", action:false},
				{id:10, icon:"fa-heartbeat", action:false},
				{id:11, icon:"fa-gavel", action:false},
				{id:12, icon:"fa-handshake-o", action:false},
				{id:13, icon:"fa-music", action:false},
				{id:14, icon:"fa-shopping-basket", action:false},
				{id:15, icon:"fa-send", action:false},
				{id:16, icon:"fa-star", action:false},
				{id:17, icon:"fa-tv", action:false},
				{id:18, icon:"fa-tree", action:false},
				{id:19, icon:"fa-rocket", action:false},
				{id:20, icon:"fa-github-alt", action:false}
				

				],
				difficulty:1,
				cardsCount:4,
				timer:'00:00',
				levelMatch:2,
				allCards:[],
				match: [],
				attempts: 0,
				matches:0,
				win:false,
				points:0,
				fliptime:""				
			}
		},

		methods:{
			flip(obj,index){

				if(this.match.length>0){
					if(obj.action==true){
						return;
					}
				}
				if(this.match.length==this.levelMatch){
					this.check();
					this.match.splice(0,2);
					clearTimeout(this.fliptime);
					
				}
				
				obj={id:obj.id, icon:obj.icon, action:true};
				
				this.allCards.splice(index,1,obj);
				
				this.match.push(obj);

				if(this.match.length==this.levelMatch){
					 this.fliptime = setTimeout( this.check,1200);
					
				}


				
			},
			check(){
				

				if(this.match[0].id==this.match[1].id ){
					this.matches++;
					this.points+=2;
					this.match.splice(0,2);
					if(this.matches==this.cardsCount){
					this.win=true;
					return;
				}

				}else {
					this.attempts++;
					for(var key=0;key<this.allCards.length;key++){
						if(this.allCards[key].id==this.match[0].id || this.allCards[key].id==this.match[1].id){
							this.allCards.splice(key,1,{id:this.allCards[key].id, icon:this.allCards[key].icon, action:false });
						}
					}
					
					this.match.splice(0,2);
					
				}
			
			},
			increaseDifficulty(){
				this.cardsCount++;
				this.addMoreCards();
				if(this.difficulty==20){
					this.cardsCount=20;
					this.addMoreCards();
				}
			},
			reshuffle(state){
				//this.allCards.push({id:11, icon:"fa-automobile"});
				

				if(state=='reset'){
					var opt = this.shuffle();
					for(var i=0;i<this.allCards.length;i++){
						this.$set(this.allCards, i, {id:opt[i].id, icon:opt[i].icon, action:false});
					}
				}else if(state=='levelup'){
					this.difficulty++;
					this.increaseDifficulty();
					var opt = this.shuffle();
					for(var i=0;i<this.allCards.length;i++){
						this.$set(this.allCards, i, {id:opt[i].id, icon:opt[i].icon, action:false});
					}
					this.save();
				}
				this.matches=0;
				this.attempts=0;
				this.win=false;
			},
			shuffle(){
				var length = this.allCards.length-1;
				var temp, random;

				while(length>=0){
					random = Math.floor(Math.random() * length+1);

					temp = this.allCards[length];
					this.allCards[length] = this.allCards[random];
					this.allCards[random] = temp;
					this.allCards[length].action = false;
					
					length--;

				}
				
				return this.allCards;
			},
			addMoreCards(){
				this.allCards=[];
				for(var matchcount=0;matchcount<this.levelMatch;matchcount++){
					for (var key=0; key<this.cardsCount; key++){
					this.allCards.push(this.cardsInfo[key]);
					}
				}
				this.shuffle();
			},
			save(){
				localStorage.setItem("cardlevel",this.difficulty);
				localStorage.setItem("cardsCount", this.cardsCount);
				localStorage.setItem("points", this.points);
			}, 
			reloadGame(){
				if(localStorage.getItem("points")!=null){
				this.points = parseInt(localStorage.getItem("points"));
				this.cardsCount = parseInt(localStorage.getItem("cardsCount"));
				this.difficulty = parseInt(localStorage.getItem("cardlevel"));
			}
			}
			
		},
		created(){

			this.reloadGame();
			
			for(var matchcount=0;matchcount<this.levelMatch;matchcount++){
				for (var key=0; key<this.cardsCount; key++){
				this.allCards.push(this.cardsInfo[key]);
				}
			}
			
			this.shuffle();
			
		}
	
}
</script>

<style scoped>

.label{padding:5px 5px; font-size:85%;}
.details{padding-bottom:30px;}
.btn-new{border-radius: 0px;box-shadow: 2px 1px 3px 1px #d3d3d3;}
.card{ display:inline-block; width:100px; height:100px; border:1px solid #f8f8f8;padding:10px; margin:5px;background-color: green; }
.cardplay{ animation-name:example; animation-duration:1s;animation-fill-mode: forwards;}
.cardplay2{  animation-name:example; animation-duration:1s;animation-fill-mode: forwards; }
.cardplay .icon{  animation-name:icon; animation-duration:1s;animation-fill-mode: forwards; }
.icon{ opacity:0; }
.fa{padding:20px;color:white;}
.cards-container{ padding-top:0px;padding-left:250px;padding-right:250px; padding-bottom:50px;}
.btn-container{ padding-top:50px;}

@keyframes example {
0% {transform:rotateY(0deg);}
50% {transform:rotateY(90deg);}
100% {transform:rotateY(180deg);}
}

@keyframes icon {
0% {opacity:0;}
45% {opacity:0;}
50% {opacity:0;}
55% {opacity:1;}
100% {opacity:1;}
}

.faded{position:fixed;top:0;left:0;width:100%;height:100%;z-index:49;background-color:#00000087;}
.win-box{position:fixed;top:10%;left:10%;width:80%;height:55%;z-index:50;background-color:white;border:1px solid gray; padding:45px 10px 25px 10px;}
.fa-trophy{color:#ffd312;font-size: 500%;}


	/*col- sm*/
	@media  (min-width: 768px)and (max-width: 991px) {
		.cards-container{
			padding-left:50px;padding-right:50px;
		}
	}

	/*col-xs*/
	@media  (max-width: 767px) {
		.card{ display:inline-block; width:15%; height:50px; border:1px solid gray;padding:2px; margin:5px;background-color: green; }
		.fa{padding:10px 0px 0px 0px;color:white;font-size: 170%;}
		.cards-container{
			width:100%;
			padding-left:0px;padding-right:0px;
		}
		.fa-trophy{color:#ffd312;font-size: 500%;}

		.win-box{position:fixed;top:10%;left:5%;width:90%;height:65%;z-index:50;background-color:white;border:1px solid gray; padding:45px 10px 25px 10px;}
	}
</style>