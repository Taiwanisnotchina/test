# test
module seven_seg_cnt0_7(
	clk,
	rst,
	CA,
	CB,
	CC,
	CD,
	CE,
	CF,
	CG,
	AN0,
	AN1,
	AN2,
	AN3,
	AN4,
	AN5,
	AN6,
	AN7
);
    
input clk;
input rst;
output CA,CB,CC,CD,CE,CF,CG;
output AN0,AN1,AN2,AN3,AN4,AN5,AN6,AN7;

reg [2:0] cnt;
reg [6:0] seg_number,seg_data;
reg [31:0] cnt_2hz;
reg clk_2hz;
wire [3:0] birth_num;

assign {AN7,AN6,AN5,AN4,AN3,AN2,AN1,AN0} = 8'b1111_1110; //turn on the 8th 7seg LED
assign {CG,CF,CE,CD,CC,CB,CA} = seg_data; 

//**CLK_DIV**//
always@ (posedge clk or posedge rst) begin
	if (rst) begin
		cnt_2hz <= 32'b0;
		clk_2hz <= 1'b0;
	end
	else begin
		if (cnt_2hz >= 25000000) begin
			cnt_2hz <= 32'b0;
			clk_2hz <= ~clk_2hz;
		end
		else begin
			cnt_2hz <= cnt_2hz + 1;
			clk_2hz <= clk_2hz;
		end
	end
end

//**COUNTER**//
always@ (posedge clk_2hz ) begin
	if (rst)begin
		cnt <= cnt + 3'b1;
		end
	else begin
		cnt <= cnt + 3'b1;
		end
end
assign	birth_num[3] = (!cnt[2] & !cnt[1] & cnt[0]) | (cnt[2] & cnt[1] & cnt[0]);
assign	birth_num[2] = !cnt[2] & cnt[1];
assign	birth_num[1] = (!cnt[2] & cnt[1]) | (cnt[2] & !cnt[1] & cnt[0]);
assign	birth_num[0] = (!cnt[2] & !cnt[1]) | (cnt[2] & cnt[0]) | (cnt[1] & !cnt[0]);	
//**BCD_to_7seg**//
always@(*) begin
    if (~rst) begin
	seg_data[0] = (cnt[2] & !cnt[1] & !cnt[0]) | (!cnt[2] & !cnt[1] & cnt[0]); 								//CA
	seg_data[1] = (cnt[2] & !cnt[1] & cnt[0]) | (cnt[2] & cnt[1] & !cnt[0]); 								//CB
	seg_data[2] = !cnt[2] & cnt[1] & !cnt[0]; 																//CC
	seg_data[3] = (cnt[2] & !cnt[1] & !cnt[0]) | (!cnt[2] & !cnt[1] & cnt[0]) | (cnt[2] & cnt[1] & cnt[0]); //CD
	seg_data[4] = (cnt[2] & !cnt[1]) | (!cnt[2] & cnt[0]) | (cnt[2] & cnt[0]); 								//CE
	seg_data[5] = (!cnt[2] & cnt[0]) | (!cnt[2] & cnt[1]); 													//CF
	seg_data[6] = (!cnt[2] & !cnt[1]) | (cnt[2] & cnt[1] & cnt[0]) ;  //CG
	end
	else begin
    seg_data[6] = (!birth_num[3] & !birth_num[2] & !birth_num[1]) | (!birth_num[3] & birth_num[2] & birth_num[1] & birth_num[0]);
    seg_data[5] = (!birth_num[3] & !birth_num[2] & birth_num[0]) | (!birth_num[3] & !birth_num[2] & birth_num[1]);
    seg_data[4] = (!birth_num[3] & birth_num[2] & !birth_num[1]) | (!birth_num[2] & !birth_num[1] & birth_num[0]) | (!birth_num[3] & birth_num[2] & birth_num[0]) |  (!birth_num[3] & birth_num[1] & birth_num[0]);
    seg_data[3] = (!birth_num[3] & birth_num[2] & !birth_num[1] & !birth_num[0]) |  (!birth_num[3] & !birth_num[2] & !birth_num[1] & birth_num[0]) |  (!birth_num[3] & birth_num[2] & birth_num[1] & birth_num[0]);
    seg_data[2] = (!birth_num[3] & !birth_num[2] & birth_num[1] & !birth_num[0]);
    seg_data[1] = (!birth_num[3] & birth_num[2] & !birth_num[1] & birth_num[0]) |  (!birth_num[3] & birth_num[2] & birth_num[1] & !birth_num[0]);     seg_data[0] = (!birth_num[3] & birth_num[2] & !birth_num[1] & !birth_num[0]) |  (!birth_num[3] & !birth_num[2] & !birth_num[1] & birth_num[0]);
	end
    end

endmodule
