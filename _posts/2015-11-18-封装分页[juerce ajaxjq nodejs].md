---
layout : post
title : "ajax,jq,node"
category : git
duoshuo: true
date : 2015-12-20
tags : ajax
---


### 模板部分：product-list-tpl.html


	<script type="text/juicer" id="product-list-tpl">
	    {@each products as product}
	    {@set statusName = '' }
	    {@set className = '' }
	    {@if product.status =='销售中' }
				{@set className = 'button-div' }
				{@set statusName ="立即购买" }
		{@else if product.status =='待上架'}
				{@set className = 'button-div button-arive' }
				{@set statusName ="预售" }
		{@else if product.status =='售完'|| products.status =='兑付中' }
				{@set className = 'button-div-saleout' }
				{@set statusName ="已售完" }
		{@else if product.status =='已兑付' }
				{@set className = 'status-p1' }
				{@set statusName ="已兑付" }
		{@/if}


<!-- more -->





### 模板部分：product-list-tpl.html


	<script type="text/juicer" id="product-list-tpl">
	    {@each products as product}
	    {@set statusName = '' }
	    {@set className = '' }
	    {@if product.status =='销售中' }
				{@set className = 'button-div' }
				{@set statusName ="立即购买" }
		{@else if product.status =='待上架'}
				{@set className = 'button-div button-arive' }
				{@set statusName ="预售" }
		{@else if product.status =='售完'|| products.status =='兑付中' }
				{@set className = 'button-div-saleout' }
				{@set statusName ="已售完" }
		{@else if product.status =='已兑付' }
				{@set className = 'status-p1' }
				{@set statusName ="已兑付" }
		{@/if}
	    <div class="product-list">
						<div class="pro-list-left pro-list-left-bj1">
							<p class="pro-p1">${product.name}</p>
							<p class="p1">年化收益率</p>
							<p class="pro-p3">${(product.rate + product.extraRate)}<span>%</span></p>
						</div>
						<div class="pro-list-right">
							<div class="right-top">
								<p class="right-top-p right-p-border">可购金额（元）<br>
									<span class="num">${product.remainAmt}</span>
								</p>
								<p class="right-top-p">理财期限（天）<br>
									<span class="num">${product.duration}</span>
								</p>
							</div>
							<div class="right-bottom">
								<form action="#" method="post">
									<div class="num-div">
										<p class="num">起购金额：${product.limitAmt}元</p>
									</div>
									<div class="${className}">
										<a href="/product/detail/${product.id}">${statusName}</a>
									</div>
								</form>
							</div>
						</div>
		</div><!--product-list end-->
	    {@/each}
	</script>
	
	
### 页面部分：


	<link rel="stylesheet" type="text/css" href="/stylesheets/productlist.css">
	<!--<link rel="stylesheet" type="text/css" href="/stylesheets/paging.css" />-->
	<div class="index-banner">
		<div class="hd">
			<ul class="num">
			</ul>
		</div>
		<div class="bd">
			<ul class="pic">
				<!--
	                作者：zhongciyisheng@live.com
	                时间：2015-11-03
	                描述：动态添加图片，要添加图片和连接，青岛addbannerimges.js文件添加
	            -->
			</ul>
		</div>
		<div class="pnBtn prev"> <span class="blackBg"></span>
			<a class="arrow" href="javascript:void(0)"></a>
		</div>
		<div class="pnBtn next"> <span class="blackBg"></span>
			<a class="arrow" href="javascript:void(0)"></a>
		</div>
	</div><!--index-banner end-->
	<div class="bank"></div><!--轮播下面的空白-->
	<div class="licai">
		<div class="full">
			<div class="licai-p">
				<p class="licai-product">理财产品</p>
			</div>
			<div id="container">
				<!--
	            	作者：zhongciyisheng@live.com
	            	时间：2015-11-18
	            	描述：装载产品列表区域，为便于后期维护查找。特意做此备注
	            -->
			</div>
			<div >
				<ul id="pagination" class="pagination-sm"></ul>
			</div>
		</div><!--full end-->
	
	</div>
	<!--licai end-->
	
	<%-partial('../template/product-list-tpl') %>
		<!--<script src="/js/jquery.pagination.js" type="text/javascript" charset="utf-8"></script>-->
		<!--<script src="/js/modules/paging-config.js" type="text/javascript" charset="utf-8"></script>-->
		<script src="/js/modules/productlist-config.js" type="text/javascript" charset="utf-8"></script>
		

### js部分


	/**
	 * Created by lrh on 17/11/15.
	 * pagination use jquery twbs-pagination plugin
	 * twbs-pagination defaults param
	 * defaults = {
	        totalPages: 1,
	        startPage: 1,
	        visiblePages: 5,
	        initiateStartPageClick: true,
	        href: false,
	        pageVariable: '{-{ pa----ge }-}',
	        totalPagesVariable: '{{total_pages}}',
	        page: null,
	        first: 'First',
	        prev: 'Previous',
	        next: 'Next',
	        last: 'Last',
	        loop: false,
	        onPageClick: null,
	        paginationClass: 'pagination',
	        nextClass: 'next',
	        prevClass: 'prev',
	        lastClass: 'last',
	        firstClass: 'first',
	        pageClass: 'page',
	        activeClass: 'active',
	        disabledClass: 'disabled'
	    }
	 */
	
	define(function(require, exports, module) {
	    require('/stylesheets/pagination.css');
	    require('jpagination');
	    require('juicer');
	
	    var pagination;
	    var currentPage = 1;
	    var cacheParam;
	    var pPlugin;
	    var defaults = {
	        first:'首页',
	        prev:'上一页',
	        next:'下一页',
	        last:'尾页',
	        startPage:1,
	        visiblePages: 5,
	        initiateStartPageClick: false,
	        onPageClick: function (event, page) {
	            currentPage = page;
	            pagination.request(cacheParam);
	        }
	    };
	
	    /**
	     *
	     * @param content 展示内容的对象
	     * @param tplId list模板的id
	     * @param url   请求数据的url
	     * @constructor
	     */
	    
	    function Pagination(content,tplId,url){
	        this.content = content;
	        this.tplId = tplId;
	        this.url = url;
	        pagination = this;
	    }
	    Pagination.prototype.withErrorCallBack = function(errorCallBack){
	        this.errorCallBack = errorCallBack;
	    }
	
	    Pagination.prototype.render = function(data){
	        this.content.html(juicer(this.tplId,data));
	    }
	
	    Pagination.prototype.request = function(param){
	        if(!cacheParam){
	            cacheParam = param||{};
	        }
	        var reqParam = $.extend({},cacheParam,{currentPage:currentPage});
	        
	        $.ajax({
	            type: "POST",
	            dataType: "jsonp",
	            url: pagination.url,
	            data: reqParam,
	            success: function (data) {
	                if(data.err_code == '0'){
	                    if(!pPlugin){
	                        pPlugin = pagination.initPage({totalPages:Number(data.page.pageNum)});
	                    }else{
	                        pPlugin.options.totalPages = Number(data.page.pageNum);
	                    }
	                    pagination.render(data.data);
	
	                }else if(pagination.errorCallBack){
	                    pagination.errorCallBack(data);
	                }
	            }
	        });
	    }
	
	    /**
	     * chu
	     * @param options
	     * @returns {*|jQuery}
	     */
	    Pagination.prototype.initPage = function(options){
	        var dp = $.extend({},defaults,options);
	        $('#pagination').parent().css('text-align','center');
	        return new $.fn.twbsPagination('#pagination',dp);
	    }
	
	    module.exports =Pagination;
	})
