# Design_and_Simulate_the_Memory_Design_using_verilog_HDL
# EXP NO:5. Design-and-Simulate the-Memory-Design-using-Verilog-HDL
# Aim 
To design and simulate a RAM,ROM,FIFO using Verilog HDL, and verify its functionality through a testbench in the Vivado 2023.1 environment. 

# Apparatus Required 
Vivado 2023.1 

# Procedure
1. Launch Vivado 2023.1 Open Vivado and create a new project.
2. Design the Verilog Code Write the Verilog code for the RAM,ROM,FIFO
3. Create the Testbench Write a testbench to simulate the memory behavior. The testbench should apply various and monitor the corresponding output.
4. Create the Verilog Files Create both the design module and the testbench in the Vivado project.
5. Run Simulation Run the behavioral simulation to verify the output.
6. Observe the Waveforms Analyze the output waveforms in the simulation window, and verify that the correct read and write operation.
7. Save and Document Results Capture screenshots of the waveform and save the simulation logs. These will be included in the lab report.

# Code
# RAM
// Verilog code
```
module ram_memory(input clk,rst,
input[7:0] data_in,input wr_en,input[9:0] address,
output reg[7:0] data_out);
reg[7:0] ram_memory[1023:0];
always@(posedge clk)
 begin 
  if(rst)
     data_out <= 8'b0;
    else if(wr_en)
    ram_memory[address] <= data_in;
    else
    data_out <= ram_memory[address];
    end
endmodule
```
// Test bench
```
module rom_design_tb;
reg clk, rst;
reg [2:0] address;
wire [3:0] dataout;

rom_design uut (
  .clk(clk),
  .rst(rst),
  .address(address),
  .dataout(dataout)
);

initial begin
  clk = 1'b0;
  forever #5 clk = ~clk;
end

initial begin
  rst = 1'b1;
  address = 3'b000;
  
  // Reset assertion
  #10 rst = 1'b0;
  
  // Test address 0
  #10 address = 3'b000;
  #10 $display("Address: %d, Dataout: %d", address, dataout);
  
  // Test address 1
  #10 address = 3'b001;
  #10 $display("Address: %d, Dataout: %d", address, dataout);
  
  // Test address 2
  #10 address = 3'b010;
  #10 $display("Address: %d, Dataout: %d", address, dataout);
  
  // Test address 4
  #10 address = 3'b100;
  #10 $display("Address: %d, Dataout: %d", address, dataout);
  
  // Test address 5
  #10 address = 3'b101;
  #10 $display("Address: %d, Dataout: %d", address, dataout);
  
  // Test address 6
  #10 address = 3'b110;
  #10 $display("Address: %d, Dataout: %d", address, dataout);
  
  // Test address 7
  #10 address = 3'b111;
  #10 $display("Address: %d, Dataout: %d", address, dataout);
  
  // Test reset
  #10 rst = 1'b1;
  #10 $display("Reset asserted, Dataout: %d", dataout);
end

endmodule
```

// output Waveform
<img width="1145" height="471" alt="image" src="https://github.com/user-attachments/assets/efbf2666-a10a-421b-a024-ea8d4db55dc3" />


# ROM
// write verilog code for ROM 
```
module rom_mem(input clk,rst,input[9:0] address,output reg[7:0] data_out);

reg [7:0] rom_mem[1023:0];

initial begin
rom_mem[0]=8'h1A;
rom_mem[1]=8'h2A;
rom_mem[2]=8'h3A;
rom_mem[3]=8'h92;
rom_mem[4]=8'h5E;
rom_mem[5]=8'h6F;
rom_mem[6]=8'h70;

end

always@(posedge clk) 
begin

if(rst)

data_out <= 8'b0;

else

data_out <= rom_mem[address];

end

endmodule
```

// Test bench
```
module rom_design_tb;
reg clk, rst;
reg [2:0] address;
wire [3:0] dataout;

rom_design uut (
  .clk(clk),
  .rst(rst),
  .address(address),
  .dataout(dataout)
);

initial begin
  clk = 1'b0;
  forever #5 clk = ~clk;
end

initial begin
  rst = 1'b1;
  address = 3'b000;
  
  // Reset assertion
  #10 rst = 1'b0;
  
  // Test address 0
  #10 address = 3'b000;
  #10 $display("Address: %d, Dataout: %d", address, dataout);
  
  // Test address 1
  #10 address = 3'b001;
  #10 $display("Address: %d, Dataout: %d", address, dataout);
  
  // Test address 2
  #10 address = 3'b010;
  #10 $display("Address: %d, Dataout: %d", address, dataout);
  
  // Test address 4
  #10 address = 3'b100;
  #10 $display("Address: %d, Dataout: %d", address, dataout);
  
  // Test address 5
  #10 address = 3'b101;
  #10 $display("Address: %d, Dataout: %d", address, dataout);
  
  // Test address 6
  #10 address = 3'b110;
  #10 $display("Address: %d, Dataout: %d", address, dataout);
  
  // Test address 7
  #10 address = 3'b111;
  #10 $display("Address: %d, Dataout: %d", address, dataout);
  
  // Test reset
  #10 rst = 1'b1;
  #10 $display("Reset asserted, Dataout: %d", dataout);
end

endmodule
```
// output Waveform
<img width="1212" height="471" alt="image" src="https://github.com/user-attachments/assets/e0941467-abfa-482f-871c-50f53b9c22c6" />


# FIFO
// write verilog code for FIFO
```
module fifo_8 #(parameter depth = 8, data_width = 8)
  (input  clk, rst, 
   input    w_en, r_en,
   input   [data_width-1:0] data_in,
   output  reg [data_width-1:0] data_out,
   output  full, empty);

reg [$clog2(depth)-1:0] w_ptr, r_ptr;
reg [data_width-1:0] fifo [depth-1:0];  // Adjusted array bounds

always @(posedge clk) begin
  if (!rst) begin
    w_ptr <= 0;
    r_ptr <= 0;
    data_out <= 0;
  end
end

always @(posedge clk) begin
  if (w_en && !full) begin
    fifo[w_ptr] <= data_in;
    w_ptr <= w_ptr + 1;  // Pointer wrap-around
  end
end

always @(posedge clk) begin
  if (r_en && !empty) begin
    data_out <= fifo[r_ptr];  // Corrected read data assignment
    r_ptr <= r_ptr + 1;  // Pointer wrap-around
  end
end

assign full = (w_ptr + 1 == r_ptr);
assign empty = (w_ptr == r_ptr);




endmodule
```

// Test bench
```
module fifo_8_tb;
reg clk, rst;
reg w_en, r_en;
reg [7:0] data_in;
wire [7:0] data_out;
wire full, empty;

fifo_8 #(.depth(8), .data_width(8)) uut (
    .clk(clk),
    .rst(rst),
    .w_en(w_en),
    .r_en(r_en),
    .data_in(data_in),
    .data_out(data_out),
    .full(full),
    .empty(empty)
);

initial begin
    clk = 1'b0;
    forever #5 clk = ~clk;
end

initial begin
    rst = 1'b1;
    w_en = 1'b0;
    r_en = 1'b0;
    data_in = 8'd0;
    
    // Reset assertion
    #10 rst = 1'b0;
    
    // Write data 1
    #10 w_en = 1'b1;
    data_in = 8'd1;
    #10;
    w_en = 1'b0;
    $display("Write 1, Full: %b, Empty: %b", full, empty);
    
    // Write data 2
    #10 w_en = 1'b1;
    data_in = 8'd2;
    #10;
    w_en = 1'b0;
    $display("Write 2, Full: %b, Empty: %b", full, empty);
    
    // Write data 3-8
    #10 w_en = 1'b1; data_in = 8'd3; #10; w_en = 1'b0;
    #10 w_en = 1'b1; data_in = 8'd4; #10; w_en = 1'b0;
    #10 w_en = 1'b1; data_in = 8'd5; #10; w_en = 1'b0;
    #10 w_en = 1'b1; data_in = 8'd6; #10; w_en = 1'b0;
    #10 w_en = 1'b1; data_in = 8'd7; #10; w_en = 1'b0;
    #10 w_en = 1'b1; data_in = 8'd8; #10; w_en = 1'b0;
    $display("Write 3-8, Full: %b, Empty: %b", full, empty);
    
    // Read data 1
    #10 r_en = 1'b1;
    #10;
    r_en = 1'b0;
    $display("Read 1, Data: %d, Full: %b, Empty: %b", data_out, full, empty);
    
    // Read data 2-8
    #10 r_en = 1'b1; #10; r_en = 1'b0;
    #10 r_en = 1'b1; #10; r_en = 1'b0;
    #10 r_en = 1'b1; #10; r_en = 1'b0;
    #10 r_en = 1'b1; #10; r_en = 1'b0;
    #10 r_en = 1'b1; #10; r_en = 1'b0;
    #10 r_en = 1'b1; #10; r_en = 1'b0;
    #10 r_en = 1'b1; #10; r_en = 1'b0;
    $display("Read 2-8, Full: %b, Empty: %b", full, empty);
    
    // Overflow test
    #10 w_en = 1'b1;
    data_in = 8'd9;
    #10;
    $display("Overflow, Full: %b, Empty: %b", full, empty);
    
    // Underflow test
    #10 r_en = 1'b1;
    #10;
    $display("Underflow, Full: %b, Empty: %b", full, empty);
end

endmodule
```

// output Waveform
<img width="1248" height="458" alt="image" src="https://github.com/user-attachments/assets/5207570a-eb69-4ef1-bcf8-0c3e8442b92a" />

# Conclusion
The RAM, ROM, FIFO memory with read and write operations was designed and successfully simulated using Verilog HDL. The testbench verified both the write and read functionalities by simulating the memory operations and observing the output waveforms. The experiment demonstrates how to implement memory operations in Verilog, effectively modeling both the reading and writing processes.
