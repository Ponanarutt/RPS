จากปัญหาที่ได้มาจะเห็นว่าซิ่งที่เราต้องเเก้มี 

    1. ไม่มีใครยอมเลือกก่อน
    2. หาที่มาของ idx(อินเด็ก) ไม่ได้ ไม่รู้ใครครอบครอง
    3. การมีเงินค้างในระบบ ในกรณีต่างๆ
      3.1 คนไม่ครบ 2 คน
      3.2 ไม่ยอมเผยไพ่ในมือ
      3.3 มีคนไม่เลือกไพ่
    4. การเริ่มใหม่

  ขั้นตอนการเเก้ปัญหา 

  ข้อที่ 1 
    ปัญหา : กลัวการถูกเห็นไพ่ในมือ
    
    การเเก้ปัญหา : 
        เราจะซ้อนไพ่ในมือของต่างฝ่ายเเล้วจะให้สัญญาณเปิดพร้อมกันทำให้ จะไม่มีใครเผยไพ่ทีหลัง

        code :
        function revealing(uint choice) public { 
          uint idx = addplay[msg.sender]; // การระบุเจ้าของ 
          reveal(bytes32(choice)); 
        
          require(numInput == 2); // จะทำเมื่อมีเพลเยอร์ครบเเล้ว
        
          peipai++; // บอกว่า player พร้อมเเล้วนะ 
          
          player[idx].choice = choice ;

          # เช็คความพร้อมของทั้งสองคน
          if(peipai == 2){
            _checkWinnerAndPay(); // ตรวจผล
          }

        }
  ข้อที่ 2 
    ปัญหา : ไม่รู้ที่มาของอินเด็ก
    
    การเเก้ปัญหา :
        เราจะทำการ mapping player กับ index นั้นเอง

        code : 
            mapping (uint => Player) public player;
            mapping (address => uint) public addplay ; // ระบุตัวเจ้าของ


  ข้อที่ 3
    ปัญหา : การที่เงื่อนไขไม่ครบทำให้เงินค้างอยู่ในระบบ
    
    การเเก้ปัญหา :
        การเเก้ปัญหาเราจะไม่รอให้เงื่อนไขครบ เเต่เราจะกำหนดเวลาการรอ ถ้าหมดเวลาจะดีดตัวออก

        code :     
	
 	function draw() public {

        	uint idx = addplay[msg.sender]; //ระบุตัวคนใช้

        	require(numPlayer == 1 || numPlayer == 2); // ต้องมีผู้เล่น
        	require(msg.sender == player[idx].addr); 
        	require(block.timestamp - player[idx].timestamp > Limiter); // ตรวจเวลา timelimit 
        	require(player[1].addr  == msg.sender || player[0].addr == msg.sender ); 
        
		# กรณีมีคนไม่ยอมเปิดไพ่
        	if (numPlayer == 2 && numInput == 2 && peipai < 2){
            		require(commits[msg.sender].revealed == true);
        	}

		#มี player เเค่คนเดียว ไม่มีคู่
        	else if (numPlayer == 1){ idx = 0; }

		# มีเพลเยอร์ไม่เลือกไพ่
        	else if (numPlayer == 2 && numInput < 2){
            		require(player[idx].input == true);
        	}

	 	# โอนเงินคืน
        	address payable wallet = payable(player[idx].addr);
        	wallet.transfer(reward);

  		#ล้างข้อมุลทิ้ง
        	clear();
    	}

  ข้อที่ 4
    ปัญหา : การเคลียข้อมูล
    
    การเเก้ปัญหา : 
        เราจะรีเซ็ทข้อมูลย้อนกลับมาทั้งหมด

    	code :
	    function clear() private  {
        
        	// รีเซ็ทข้อมูล player 
        	address account0 = player[0].addr;
        	address account1 = player[1].addr;

        	delete addplay[account0];
        	delete addplay[account1];

        	delete player[0];
        	delete player[1];

        	// เซ็ทข้อมูลกลับ
        	numPlayer = 0 ;
        	numInput = 0 ;
        	reward = 0 ;
        	peipai = 0 ;
    	}

---      การเเก้ปัญหาที่กล่าวมาข้างต้นเป็นการเเก้ปัญหาจากโค้ทที่อาจารย์ได้กำหนดมา     ---

หลักการคิดหาคนชนะ จากเเผนภาพการเเพ้ชนะเราจะเห็นว่า เราะจะเสมอตัวเอง , เราจะชนะ 3 อันดับต่อจากเรา เเละ เราจะเเพ้ 3 อันดับหลังเรา

	function _checkWinnerAndPay() private {

        	uint p0Choice = player[0].choice;
        	uint p1Choice = player[1].choice;

        	address payable account0 = payable(player[0].addr);
        	address payable account1 = payable(player[1].addr);
        
        	//เสมอทำก่อนจะได้ไม่ต้องเสียเวลาคำณวน
        	if (p0Choice == p1Choice){
            		account0.transfer(reward/2);
            		account1.transfer(reward/2);
       	 	}

        	//p0 เเพ้ ตรวจการเเพ้ 3 อันดับก่อน
        	else if(p0Choice-1 % 7 == p1Choice || p0Choice-2 % 7 == p1Choice || p0Choice-3 % 7 == p1Choice){
            		account1.transfer(reward);
        	}

        	//p0 ชนะ 
        	else{
            		account0.transfer(reward);
        	}
    	
     }




   
