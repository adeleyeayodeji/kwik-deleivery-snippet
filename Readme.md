```php
	      date_default_timezone_set("Africa/Lagos");
		 		$todaydate =  date('Y-m-d H:i:s', time());
		 		$pickup_date = date('Y-m-d H:i:s', strtotime($todaydate . ' +2 hours'));
		 		$delivery_date = date('Y-m-d H:i:s', strtotime($todaydate . ' +2 hours'));
				
		 		$api = wc_kwik_delivery()->get_api();
				
		 		$pickups = array(
                 array(
		 		"name" => $store_info["store_name"],
		 		"phone" => $store_user->get_shop_info()["customer_support"]["phone"],
		 		"address" => $store_address,
		 		"latitude" => $store_lat,
		 		"longitude" => $store_lng,
		 		"time" => $pickup_date
                 )
		 		);
				
		 		$deliveries = array(
                 array(
		 		"name" => $receiver_name,
		 		"phone" => $receiver_phone,
		 		"email" => $receiver_email,
		 		"address" => $delivery_address,
		 		"latitude" =>  $delivery_coordinate_lat,
		 		"longitude" =>   $delivery_coordinate_lng,
		 		"time" => $delivery_date
                 )
		 		);
                 //If data found in base
                if(get_option($vendor_id."KwikShipping")){
                    
                    $customshipping = json_decode(get_option($vendor_id."KwikShipping"));
                    
                     if($payment_method == 'cod') {
                        
                         $params = array(
                             'pickups'               => $pickups,
                             'deliveries'            => $deliveries,
                             'insurance_amount'      => $customshipping->insurance_amount,
                             'total_no_of_tasks'     => $customshipping->total_no_of_tasks,
                             'total_service_charge'  => $customshipping->total_service_charge,
                             'amount'                => $order->get_total(),
                             'payment_method'        => $payment_methodkwik
                             );
                        
                         }else {
                        
                         $params = array(
                             'pickups'               => $pickups,
                             'deliveries'            => $deliveries,
                             'insurance_amount'      => $customshipping->insurance_amount,
                             'total_no_of_tasks'     => $customshipping->total_no_of_tasks,
                             'total_service_charge'  => $customshipping->total_service_charge,
                             'amount'                => $customshipping->per_task_cost,
                             'payment_method'        => $payment_methodkwik
                         );
                        
                     }
                    
                     //Delete data
                     delete_option($vendor_id."KwikShipping");
                    
                     // error_log(print_r($params, true));
                     $res = $api->create_task($params);
                     // error_log(print_r($res, true));
                    
                     $order->add_order_note("Kwik Delivery: " . $res['message']);
                    
                     if ($res['status'] == 200) {

```
