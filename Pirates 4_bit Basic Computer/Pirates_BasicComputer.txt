module Pirates_BasicComputer(IR,TR,DR,AC,PC,AR,T,clk);


input clk;
output reg [7:0] IR,TR,DR,AC;
output reg [3:0] PC,AR;
output reg [3:0] T;   
reg [4:0] MEM [15:0];       //16 Location, each one is 8 bits
reg I;                                  // indirect_bit
reg E;                                 //Extended_register(for_Cout)
reg S;                                 //start-stop ff

 

initial begin
T <= 0;
IR <= 0;
TR <= 0;
DR <= 0;
AC <= 0;
PC <= 0;
AR <=0;
I <=0;
E <=0;
S <= 1; 
$readmemh("C:/Users/Nagawy/Desktop/Pirates/memmory_data.txt", MEM);
end

always @(posedge clk) begin 

	//fetch
	if (T == 0) begin	
		AR <= PC;
		T <= T + 1; 
                                   end
	
	else if (T == 1) begin
		IR <= MEM[AR]; 
		PC <= PC + 1;		
		T <= T + 1; 
                                            end
	
	else if (T == 2) begin
		AR <= IR[3:0];              
		I <= IR[7];
		T <= T +1; 
                                           end

	//fetched_is_done
	
	else begin
		if (IR[6:4] == 3'b111) begin
				
			if(I == 0) begin                                         //Register_reference_instructions
				
				if(AR == 4'b1000) begin                    //CLA
					AC <= 0;
					T <= 0; 
					end
					
				else if(AR == 4'b0100) begin            //SZA
					if(AC == 8'h00) begin
					PC <= PC +1;
					T <= 0; 
					end
					end
					
				else if(AR == 4'b0010) begin             //CMA
					AC <= ~AC;
					T <= 0; 
					end
					
				else if(AR == 4'b0001) begin             //HLT
					S <= 0;
					T <= 0; 
					end
				
			
		           end
		end	


	
		else begin                                          //Memory_reference_instructions

			if(I == 1) begin                                  //indirect
				if (T == 3) begin
					AR <= MEM[AR]; 
					T <= T + 1; 
					end

				else begin
					if(IR[6:4] == 3'b000) begin                                //AND
						if(T == 4) begin
							DR <= MEM[AR]; 
							T <= T + 1; end
						else begin
							AC <= (AC & DR);
							T <= 0; 
							end
					end


					else if(IR[6:4] == 3'b001) begin                         //ADD 
						if(T == 4) begin
							DR <= MEM[AR]; 
							T <= T + 1; 
							end
						else begin
							{E,AC} <= AC + DR;
							T <= 0; 
							end
					end
					else if(IR[6:4] == 3'b010) begin                      //LDA
						if(T == 4) begin
							DR <= MEM[AR]; 
							T <= T + 1; end
						else begin
							AC <= DR;
							T <= 0; 
							end
					end
		
					else if(IR[6:4] == 3'b011) begin                   //STA
						MEM[AR] <= AC ; 
						T <= 0;
						end
				   end
			   end
			end



			if(I == 0) begin                                       // direct
				if (T == 3)
				T <= T + 1;                             //nothing_to_do
				else begin
					
					if(IR[6:4] == 3'b000) begin                                //AND
						if(T == 4) begin
							DR <= MEM[AR]; 
							T <= T + 1; end
						else begin
							AC <= (AC & DR);
							T <= 0; 
							end
					end


					else if(IR[6:4] == 3'b001) begin                         //ADD 
						if(T == 4) begin
							DR <= MEM[AR]; 
							T <= T + 1; 
							end
						else begin
							{E,AC} <= AC + DR;
							T <= 0; 
							end
					end
					else if(IR[6:4] == 3'b010) begin                      //LDA
						if(T == 4) begin
							DR <= MEM[AR]; 
							T <= T + 1; end
						else begin
							AC <= DR;
							T <= 0; 
							end
					end
		
					else if(IR[6:4] == 3'b011) begin                   //STA
						MEM[AR] <= AC ; 
						T <= 0;
						end

					end
				end
			end
		end 		

endmodule		
		
		
			