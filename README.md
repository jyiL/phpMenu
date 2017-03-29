###Menu类

1 方法calculate, 输入订单后(json字符串，结构如上题), 输出格价
2 方法discount, 可设定折扣，输出格价时自动计算折扣
3 静态方法counter。输出calculate方法被调用的次数

`<?php

	// namespace Exam;

	/**
	 * Menu类
	 * @author ljy
	 * @date 2017/03/28
	 */
	class Menu {

		private $menu = '[
				{"type_id":1,"name":"大菜","food":[
												{"food_id":1,"name":"鱼香肉丝","price":"10"},
												{"food_id":2,"name":"红烧肉","price":"11"},
												{"food_id":3,"name":"香辣粉","price":"12"}
												]},
				{"type_id":2,"name":"中菜","food":[
												{"food_id":4,"name":"小炒肉","price":"13"},
												{"food_id":5,"name":"云吞","price":"14"}
												]},
				{"type_id":3,"name":"小菜","food":[
												{"food_id":6,"name":"雪糕","price":"15"},
												{"food_id":7,"name":"黄瓜","price":"16"}
												]}	    
			]';
		private $food = '';	
		private $total = '';	

		/**
		 * 初始化菜单
		 */	
		function __construct() {
			$this->food = json_decode($this->menu,1);
		}

		/**
		 * 获取菜单列表
		 * @return array
		 */
		public function foodMenu() {
			return json_decode($this->menu,1);
		}

		/**
		 * 计算订单价格
		 * @param string $order 订单
		 * @return int 
		 */	
		public function calculate($order) {
			@file_put_contents('counter', 1, FILE_APPEND);
			if(!isset($order)) return false;

			$order = json_decode(strval($order),1);

			if(!$order) return false;

			foreach ($this->food as $value) {
				foreach ($value['food'] as $val) {
					$food[] = $val;
				}
			}

			foreach ($food as $value) {
				foreach ($order as $val) {
					// var_dump(array_intersect_key($val,$value));exit;
					if($val['food_id'] === $value['food_id']) {
						$price = $value['price'];			// 单价
						$num = $val['num'];					// 数量
						$this->total += $price * $num;		// 求和
					}

				}
			}

			/** 计算折扣 **/
			@$discount = file_get_contents('discount');
			if($discount && !empty($discount)) {
				$this->total = $this->total * $discount;
			}
			return $this->total;
		}

		/**
	     * 设定折扣
	     * @param string $tmp
	     * @return int
		 */
		public function discount($tmp) {
			if(!isset($tmp)) return false;

			$tmp = str_replace('%', '', $tmp);
			$tmp =  $tmp / 100;
			@file_put_contents('discount', $tmp);

			return true;
		}

		/**
		 * 输出calculate方法被调用的次数
		 * @return int
		 */
		public static function counter() {
			@$num = file_get_contents('counter');
			return strlen($num);
		}
	}`