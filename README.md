# react-sass-webpack-doc
> **Note:**
> - 我们的目的是通过各个模块的组件化达到超强的复用性，举例说明，我们将所有组件按照功能划分，在通过不同的webpack入口文件进行打包以实现我们的 **跨平台、跨站点**的复用，闲话少说，直接撸代码。
```javascript
var setting = {
  //多个入口文件
  entry : {
    'base' : '/js/base'
		,'home' : '/js/home'
  }
  ,cache: true //增加编译速度
  ,output: {
		filename: './js/[name].bundle.js?v=[chunkhash]',
		chunkFilename: "./js/[chunkhash].js",
		path : 'dist/',
    publicPath : '/static/dist/'
	}
  //需要module模块，不用做过多解释了，通过这个去编译sass、image、react、babel等等咯
  ,module : {
		loaders : [
			{ test: /\.css$/, loader: ExtractTextPlugin.extract("style-loader", "css-loader")}
			,{test: /\.(jpe?g|png|gif)$/i, loaders: [
				'url-loader?limit=8192&name=[path][name].[ext]?[hash]'
				,'image-webpack?{progressive:true, optimizationLevel: 7, interlaced: false, pngquant:{quality: "65-90", speed: 4}}'
			]}
			,{ test: /\.scss$/, loader: ExtractTextPlugin.extract("style", "css!sass?config=sassConfig")}
			,{test: /\.(ttf|eot|svg|woff(2)?)(\?[a-z0-9]+)?$/,loader : 'url-loader?limit=50000&name=[path][name].[ext]?[hash]'}
			,{test: /\.jsx?$/, exclude: /(node_modules|bower_components)/
				,loader : 'babel-loader'
				,query : {
					presets : ['react','es2015']
					,plugins : ['transform-runtime']
				}
			}
			,{test : /\.json$/,loader: 'json'}
		]
	}
  //这个有点坑，sass动态化后在进行除法运算的时候，指定保留小数点的位数，如果不做设置，有可能会差出1px，e.g. 2/3*100，这种计算就可能出现一像素的差别来
  ,sassConfig : {
		precision : 10
	}
  ,plugins: [
    //用到两次以上的代码就会打包到vendor里去
		new webpack.optimize.CommonsChunkPlugin({
			name : 'commons'
			,filename : 'vendor.js?v=[chunkhash]'
			,minChunks : 2
		}),
    
		new webpack.DefinePlugin({
			'process.env':{
				'NODE_ENV': JSON.stringify('production')
			}
		}),
		new ExtractTextPlugin("./css/[name].css?v=[chunkhash]",{
			allChunks: true
		})
    //全局化一些经常用到的变量
		,new webpack.ProvidePlugin({
			$: "jquery",
			jQuery: "jquery",
			"window.jQuery": "jquery"
			,React : 'react'
			,ReactDOM : 'react-dom'
		})
		,new webpack.optimize.OccurenceOrderPlugin()
    //生成带版本号的asstes.json文件
		,new AssetsPlugin({filename: 'assets.json',fullPath: false,prettyPrint: true,update : true})
	]
}
```
好吧，到现在我还没说到底怎么**跨平台、跨站点**呢，暂时留到下一章节吧。






