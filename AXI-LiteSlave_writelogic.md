module axi_lite_aes_regs (
    input  wire        clk,
    input  wire        rst,

    // AXI-Lite write address
    input  wire [31:0] awaddr,
    input  wire        awvalid,
    output reg         awready,

    // AXI-Lite write data
    input  wire [31:0] wdata,
    input  wire        wvalid,
    output reg         wready,

    // Write response
    output reg  [1:0]  bresp,
    output reg         bvalid,
    input  wire        bready,

    // Outputs to AES core
    output reg [127:0] aes_key,
    output reg [95:0]  aes_iv,
    output reg [127:0] plaintext_block,
    output reg         block_valid
);

    // Address decoding
    localparam KEY0   = 32'h00;
    localparam KEY1   = 32'h04;
    localparam KEY2   = 32'h08;
    localparam KEY3   = 32'h0C;

    localparam IV0    = 32'h10;
    localparam IV1    = 32'h14;
    localparam IV2    = 32'h18;

    localparam DATA   = 32'h40;

    reg [1:0] word_count;

    always @(posedge clk) begin
        if (rst) begin
            awready        <= 0;
            wready         <= 0;
            bvalid         <= 0;
            bresp          <= 2'b00;
            word_count     <= 0;
            plaintext_block<= 0;
            block_valid    <= 0;
        end else begin
            awready <= awvalid;
            wready  <= wvalid;

            if (awvalid && wvalid) begin
                case (awaddr)
                    KEY0: aes_key[127:96] <= wdata;
                    KEY1: aes_key[95:64]  <= wdata;
                    KEY2: aes_key[63:32]  <= wdata;
                    KEY3: aes_key[31:0]   <= wdata;

                    IV0:  aes_iv[95:64]   <= wdata;
                    IV1:  aes_iv[63:32]   <= wdata;
                    IV2:  aes_iv[31:0]    <= wdata;

                    DATA: begin
                        plaintext_block <= {plaintext_block[95:0], wdata};
                        word_count <= word_count + 1;

                        if (word_count == 2'd3) begin
                            block_valid <= 1;
                            word_count  <= 0;
                        end else begin
                            block_valid <= 0;
                        end
                    end
                endcase

                bvalid <= 1;
            end

            if (bready)
                bvalid <= 0;
        end
    end

endmodule   

module tb_axi_lite_aes_regs;

    reg clk = 0;
    always #5 clk = ~clk;

    reg rst;

    reg [31:0] awaddr;
    reg awvalid;
    reg [31:0] wdata;
    reg wvalid;
    reg bready;

    wire awready, wready, bvalid;
    wire [1:0] bresp;

    wire [127:0] plaintext_block;
    wire block_valid;

    axi_lite_aes_regs dut (
        .clk(clk),
        .rst(rst),
        .awaddr(awaddr),
        .awvalid(awvalid),
        .awready(awready),
        .wdata(wdata),
        .wvalid(wvalid),
        .wready(wready),
        .bresp(bresp),
        .bvalid(bvalid),
        .bready(bready),
        .plaintext_block(plaintext_block),
        .block_valid(block_valid)
    );

    task axi_write(input [31:0] addr, input [31:0] data);
    begin
        awaddr  = addr;
        awvalid = 1;
        wdata   = data;
        wvalid  = 1;
        bready  = 1;
        #10;
        awvalid = 0;
        wvalid  = 0;
        #10;
    end
    endtask

    initial begin
        rst = 1;
        #20 rst = 0;

        axi_write(32'h40, 32'h6BC1BEE2);
        axi_write(32'h40, 32'h2E409F96);
        axi_write(32'h40, 32'hE93D7E11);
        axi_write(32'h40, 32'h7393172A);

        #50;
        $display("Plaintext block = %h", plaintext_block);
        $stop;
    end
endmodule
"UNDERSTAND EACH LINE OF THE CODE...ALSO NOT ONLY THIS 128 BIT PACKING LOGIC, I HAVE TO SEND THE KEY AND IV AS WELL NA .. SO SHOULD I INCLUDE THAT IN THIS CODE OR ANOTHER CODE"
