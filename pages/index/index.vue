<template>
	<view class="content">
		<yingbing-audio
		ref="audio"
		:playList="playList"
		:paused="paused"
		:playMode="playMode"
		:lyricModel="lyricModel"
		:lyricDefaultTitle="title"
		@currentChange="currentChange"
		@lyricChange="lyricChange"
		@onCanplay="onCanplay"
		@onPlay="onPlay"
		@onPause="onPause"
		@onEnded="onEnded"
		@onStop="onStop"
		@onError="onErr"
		@onSeeking="onSeeking"
		@onSeeked="onSeeked"
		@onTimeUpdate="onTimeUpdate"
		></yingbing-audio>
		<view class="flex">
			<button class="btn" @click="paused = false">播放</button>
			<button class="btn" @click="paused = true">暂停</button>
			<button class="btn" @click="prev">上一首</button>
			<button class="btn" @click="next">下一首</button>
			<button class="btn" @click="lyricModel = 'overall'">显示歌词</button>
			<button class="btn" @click="lyricModel = 'close'">关闭歌词</button>
			<button class="btn" @click="switchMode">播放模式：{{playMode == 'round' ? '顺序播放' : playMode == 'random' ? '随机播放' : '单曲循环'}}</button>
			<button class="btn" @click="seek">跳到1分钟</button>
			<button class="btn" @click="change(10001)">切换到第二首歌</button>
			<button class="btn" @click="deleteSong(id)">删除正在播放的歌曲</button>
			<button class="btn" @click="insert">添加歌曲</button>
			<button class="btn" @click="switchAlbum">替换歌单</button>
			<button class="btn" @click="playList = []">清空列表</button>
		</view>
		<view class="list">
			<view>当前位置：{{currentTime}}</view>
			<view>总长度：{{duration}}</view>
			<view>歌名：{{title}}</view>
			<view>歌手：{{singer}}</view>
			<view style="margin-top: 50rpx;"></view>
			<view v-for="(item, index) in playList" :key="item.id" :style="{color: item.name == title ? 'red' : 'black'}">{{item.name}}</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				playList: [{
					id: '10000',
					name: '一梦千朝',
					singer: '洛天依',
					src: '/static/001.m4a'
				},{
					id: '10001',
					name: '世末歌者',
					singer: '洛天依',
				},{
					id: '10002',
					name: '大氿歌',
					singer: '洛天依',
				}],
				srcs: [{
					name: '一梦千朝',
					src: '/static/001.m4a'
				},{
					name: '世末歌者',
					src: '/static/002.m4a'
				},{
					name: '大氿歌',
					src: '/static/003.m4a'
				},{
					name: '白石溪',
					src: '/static/004.m4a'
				},{
					name: '神女别',
					src: '/static/005.m4a'
				},{
					name: '霜雪千年',
					src: '/static/006.m4a'
				}],
				newList: [{
					id: '10003',
					name: '白石溪',
					singer: '洛天依',
				},{
					id: '10004',
					name: '神女别',
					singer: '洛天依',
				},{
					id: '10005',
					name: '霜雪千年',
					singer: '洛天依',
				}],
				lyrics: [{
					name: '大氿歌',
					lyric: '[00:00.000] 作词 : ilem↵[00:01.000] 作曲 : ilem↵[00:04.753]土生木酿水中火↵[00:09.806]金樽玉液小乾坤↵[00:14.120]文痴武客三点血↵[00:18.887]江湖相见半盏春↵[00:23.437]↵[00:23.838]一白忘忧再消愁↵[00:28.558]三碗同天竞风流↵[00:33.288]浮云苍狗烂柯泥↵[00:38.136]唯此醪糟诚不欺↵[00:42.713]↵[00:43.286]花枪风雪挑葫芦↵[00:47.740]哨棒过岗打猛虎↵[00:52.537]谪仙对影捞玉蟾↵[00:57.388]诗圣放歌击浪还↵[01:01.469]↵[01:01.562]大瓮一扬倾江海↵[01:06.264]饮日吞月胸中来↵[01:10.979]大梦一场三千载↵[01:15.678]悲喜穿肠莫挂怀↵[01:20.484]↵[01:20.830]大风翕张浪形骸↵[01:25.315]疏狂放歌死便埋↵[01:30.097]大疯一趟两相忘↵[01:34.963]不知东方天既白↵[01:40.662]↵[01:59.397]一白忘忧再消愁↵[02:04.396]三碗同天竞风流↵[02:09.297]浮云苍狗烂柯泥↵[02:14.081]唯此醪糟诚不欺↵[02:18.782]↵[02:19.233]羲之流觞笔抒怀↵[02:23.717]琅琊太守伛偻来↵[02:28.417]关公壮行斩华雄↵[02:33.287]悟空借胆闹天宫↵[02:37.651]↵[02:37.867]大瓮一扬倾江海↵[02:42.072]饮日吞月胸中来↵[02:47.036]大梦一场三千载↵[02:51.636]悲喜穿肠莫挂怀↵[02:56.486]↵[02:56.786]大风翕张浪形骸↵[03:01.271]疏狂放歌死便埋↵[03:06.019]大疯一趟两相忘↵[03:10.871]不知东方天既白↵[03:16.121]↵[03:17.772]混音：Tolein↵[03:18.968]↵'
				},{
					name: '霜雪千年',
					lyric: '[00:00.000] 作词 : COP↵[00:01.000] 作曲 : COP↵[00:02.000] 编曲 : COP↵[00:14.59]梨花香↵[00:16.27]缠着衣角掠过熙攘↵[00:20.12]复悄入红帘深帐↵[00:23.47]听枝头黄鹂逗趣儿↵[00:25.61]细风绕指淌↵[00:28.16]坐船舫↵[00:29.90]兰桨拨开雾霭迷茫↵[00:33.68]不觉已一日过半↵[00:37.08]过眼的葱郁风光↵[00:39.31]悉数泛了黄↵[00:42.18]褪尽温度的风↵[00:44.22]无言牵引中↵[00:45.63]便清晰了在此的眉目↵[00:49.54]暮色的消融↵[00:50.92]隐约了晦朔葱茏↵[00:54.57]在这老街回眸↵[00:57.45]烟云中追溯我是谁↵[00:59.28]只消暮雨点滴↵[01:00.85]便足以粉饰这是非↵[01:02.68]待这月色涌起↵[01:06.09]谁人轻叩这门扉↵[01:09.48]苔绿青石板街↵[01:10.94]斑驳了流水般岁月↵[01:12.91]小酌三盏两杯↵[01:14.39]理不清缠绕的情结↵[01:16.42]在你淡漠眉间↵[01:19.73]瞥见离人的喜悲霜雪↵[01:36.41]楼阁现↵[01:38.14]尘飞雾散荧光翩跹↵[01:41.96]显露出斑驳石阶↵[01:45.30]入眼是落英纷然↵[01:47.38]芳草入深院↵[01:49.72]凭栏杆↵[01:51.70]小桌上置琼觞两盏↵[01:55.54]阖眼听清风疏叶↵[01:57.93]似曾有欢声笑言↵[02:01.69]萦绕这高轩↵[02:04.55]云动寂静鸣蝉↵[02:06.58]雨坠激漪涟↵[02:08.00]皴擦点染勾勒这世间↵[02:12.05]缘起的一眼↵[02:13.42]定格了三生千年↵[02:16.99]在这老街回眸↵[02:19.73]烟云中追溯我是谁↵[02:21.66]只消暮雨点滴↵[02:23.24]便足以粉饰这是非↵[02:25.07]待这月色涌起↵[02:28.47]谁人轻叩这门扉↵[02:31.83]苔绿青石板街↵[02:33.40]斑驳了流水般岁月↵[02:35.34]小酌三盏两杯↵[02:36.76]理不清缠绕的情结↵[02:38.67]在你淡漠眉间↵[02:42.27]瞥见离人的喜悲霜雪↵[02:59.33]三月梨花雪↵[03:00.96]几载开了又败↵[03:02.78]笔锋走黑白↵[03:04.39]丹青中穿插无奈↵[03:06.08]彼时那弯儿月↵[03:07.86]何时初现于江畔↵[03:09.40]而我又在待何人↵[03:11.89]在这亭台 回眸↵[03:14.32]千年后忆起你是谁↵[03:16.41]只消月色隐约↵[03:17.58]便足以勾勒这是非↵[03:20.20]待这回忆涌起↵[03:23.46]恍惚之间已下泪↵[03:26.73]枫红十里长街↵[03:28.41]红帘后谁人蹙着眉↵[03:30.23]遥梦桑竹桃源↵[03:31.65]轮回中曾道别的地点↵[03:33.87]愿今生再相见↵[03:37.20]消融你眉间悲戚霜雪↵'
				},{
					name: '世末歌者',
					lyric: '[00:00.000] 作词 : COP↵[00:00.000] 作曲 : COP↵[00:00.00]编调：COP↵[00:01.00]唱：乐正绫 洛天依↵[00:17.23]蝉时雨 化成淡墨渲染暮色↵[00:17.63]渗透着 勾勒出足迹与车辙↵[00:22.78]欢笑声 与漂浮的水汽饱和↵[00:27.89]隔着窗 同城市一并模糊了↵[00:32.98]拨弄着 旧吉他 哼着四拍子的歌↵[00:38.02]回音中 一个人 仿佛颇悠然自得↵[00:43.06]等凉雨 的温度 将不安燥热中和↵[00:48.26]寻觅着 风的波折↵[00:51.93]我仍然在无人问津的阴雨霉湿之地↵[00:57.62]和着雨音 唱着没有听众的歌曲↵[01:02.53]人潮仍是漫无目的地向目的地散去↵[01:08.45]忙碌着 无为着 继续↵[01:12.88]等待着谁能够将我的心房轻轻叩击↵[01:17.92]即使是你 也仅仅驻足了片刻便离去↵[01:23.10]想着或许 下个路口会有谁与我相遇↵[01:28.86]哪怕只 一瞬的 奇迹↵[01:54.77]夏夜空 出现在遥远的记忆↵[01:59.84]绽放的 璀璨花火拥着繁星↵[02:04.95]消失前 做出最温柔的给予↵[02:10.00]一如那些模糊身影的别离↵[02:15.09]困惑地 拘束着 如城市池中之鱼↵[02:20.13]或哽咽 或低泣 都融进了泡沫里↵[02:25.25]拖曳疲惫身躯 沉入冰冷的池底↵[02:30.32]注视着 色彩褪去↵[02:34.62]我仍然在无人问津的阴雨霉湿之地↵[02:39.66]和着雨音 唱着没有听众的歌曲↵[02:44.33]人潮仍是漫无目的地向目的地散去↵[02:50.59]忙碌着 无为着 继续↵[02:54.94]祈求着谁能够将我的心房轻轻叩击↵[03:00.05]今天的你是否会留意并尝试去靠近↵[03:05.15]因为或许 下个路口仍是同样的结局↵[03:10.91]不存在 刹那的 奇迹↵[03:17.68]极夜与永昼↵[03:22.01]别离与欢聚↵[03:27.54]脉搏与呼吸↵[03:32.63]找寻着意义↵[03:39.65]我仍然在无人问津的阴雨霉湿之地↵[03:43.91]和着雨音 唱着卖不出去的歌曲↵[03:48.72]浮游之人也挣扎不已执着存在下去↵[03:54.37]追逐着 梦想着 继续↵[03:58.81]请别让我独自匍匐于滂沱世末之雨↵[04:03.88]和着雨音 唱着见证终结的歌曲↵[04:08.92]人们终于 结束了寻觅呆滞伫立原地↵[04:14.76]哭泣着 乞求着 奇迹↵[04:19.28]用这双手 拨出残缺染了锈迹的弦音↵[04:24.36]都隐没于淋漓的雨幕无声无息↵[04:29.35]曲终之时 你是否便会回应我的心音↵[04:35.01]将颤抖的双手牵起↵[04:39.76]迎来每个人的结局↵'
				}],
				paused: true,
				currentTime: 0,
				duration: 0,
				playMode: 'round',
				lyricModel: 'overall',
				title: '',
				singer: '',
				id: ''
			}
		},
		methods: {
			currentChange (e, callback) {
				this.title = e.name
				this.singer = e.singer
				this.id = e.id
				let src = ''
				let lyric = null
				if ( !e.src ) {
					let index = this.srcs.findIndex(src => src.name == e.name)
					index > -1 ? src = this.srcs[index].src : null
				}
				if ( !e.lyric ) {
					let index = this.lyrics.findIndex(lyric => lyric.name == e.name)
					index > -1 ? lyric = this.handleLyric(this.lyrics[index].lyric) : null
				}
				setTimeout(() => {
					callback({
						src: src,
						lyric: lyric
					})
				}, 500)
			},
			lyricChange (e) {
				// console.log(e);
			},
			onCanplay (e) {
				console.log(e);
				this.duration = e.duration
			},
			onPlay () {
				this.paused = false
			},
			onPause () {
				this.paused = true
			},
			onEnded () {
				console.log('播放结束');
			},
			onStop () {
				console.log('播放停止');
			},
			onErr () {
				console.log('播放失败');
			},
			onSeeking (e) {
				console.log(e);
			},
			onSeeked (e) {
				console.log(e);
			},
			onTimeUpdate (e) {
				this.currentTime = e
			},
			next () {
				this.$refs.audio.next()
			},
			prev () {
				this.$refs.audio.prev()
			},
			seek () {
				this.$refs.audio.seek(60)
			},
			change (id) {
				this.$refs.audio.change(id)
			},
			insert () {
				this.playList.length <= 4 ? this.playList = this.playList.concat(this.newList) : null
			},
			deleteSong (id) {
				let index = this.playList.findIndex(song => song.id == id)
				index > -1 ? this.playList.splice(index, 1) : null
			},
			deleteSongs () {
				this.playList = this.playList.filter((item, key) => key > 1)
			},
			switchAlbum () {
				this.playList = this.newList
				this.change(10003)
			},
			switchMode () {
				switch ( this.playMode ) {
					case 'round':
						this.playMode = 'loop'
						break
					case 'loop':
						this.playMode = 'random'
						break
					default:
						this.playMode = 'round'
						break
				}
			},
			handleLyric (str) {
				let arr = str.split('↵');
				let lyric = []
				arr.forEach(item => {
					let time = item.match(/\[(\S*)\]/) ? item.match(/\[(\S*)\]/)[0] : '';
					let title = item.split(']')[1];
					if (title && time) {
						lyric.push({
							time: this.time2seconds(time.substring(1, time.length - 1)),
							title: title
						})
					}
				})
				return lyric
			},
			time2seconds (time) {
				const seconds = parseInt(time.split(':')[0]) * 60 + parseInt(time.split(':')[1].split('.')[0]) + parseInt(time.split(':')[1].split('.')[1]) / 1000;
				return seconds; 
			}
		}
	}
</script>

<style>
	.flex {
		display: flex;
		flex-wrap: wrap;
	}
	.btn {
		margin-top: 20rpx;
	}
	.list {
		text-align: center;
		margin-top: 20rpx;
	}
</style>
