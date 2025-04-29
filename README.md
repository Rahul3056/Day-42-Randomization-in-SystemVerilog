### **Day 42: Randomization in SystemVerilog**

### **1. Overview**

**Randomization** in SystemVerilog is a powerful feature used to create **constrained-random stimulus**. It’s primarily used in **verification**, especially in **testbenches**, to automatically generate a wide range of valid input scenarios.

There are two types of randomization:
- **Unconstrained randomization** – no rules, fully random.
- **Constrained randomization** – generates values within defined limits or conditions.

---

### **2. Unconstrained Randomization**

You can use built-in functions like `$random` and `$urandom`:

```systemverilog
int a;
a = $random;        // Signed random 32-bit value
a = $urandom;       // Unsigned random 32-bit value
a = $urandom_range(10, 20); // Random between 10 and 20
```

These are **non-repeatable** across simulations unless a seed is specified.

---

### **3. Constrained Randomization with `rand` and `constraint`**

SystemVerilog allows **object-oriented randomization** using:
- `rand` or `randc` (random cyclic)
- `constraint` blocks

---

### **4. Defining a Class with Random Variables**

```systemverilog
class Packet;
    rand bit [7:0] addr;
    rand bit [7:0] data;

    constraint valid_range {
        addr > 5;
        addr < 100;
        data inside {[10:200]};
    }
endclass
```

---

### **5. Using the Class and Calling `randomize()`**

```systemverilog
module tb;

    initial begin
        Packet pkt = new();
        
        repeat (5) begin
            if (pkt.randomize()) begin
                $display("Randomized Packet: addr = %0d, data = %0d", pkt.addr, pkt.data);
            end else begin
                $display("Randomization failed");
            end
        end
    end

endmodule
```

---

### **6. Types of Random Variables**

| Keyword | Description |
|---------|-------------|
| `rand`  | Randomized normally |
| `randc` | Randomized cyclically (non-repeating until all values are used) |

---

### **7. Common Constraint Operators**

| Operator      | Meaning                  |
|---------------|---------------------------|
| `==`, `!=`, `<`, `>` | Comparison           |
| `inside`      | Value in a list/range     |
| `dist`        | Weighted distribution     |
| `&&`, `||`    | Logical AND/OR            |

---

### **8. Constraint Example: Distribution**

```systemverilog
constraint data_weighted {
    data dist {100 := 70, 200 := 30}; // 70% 100, 30% 200
}
```

---

### **9. Randomization with Arrays**

You can randomize arrays too:

```systemverilog
class ArrayTest;
    rand bit [7:0] arr[5];

    constraint valid_vals {
        foreach (arr[i]) {
            arr[i] inside {[0:50]};
        }
    }
endclass
```

---

### **10. Summary**

- Use `rand`/`randc` and `randomize()` to generate random test data.
- Use `constraint` blocks to control the valid ranges or combinations.
- Helps create **rich stimulus** and **improve coverage**.
- Essential in **UVM and advanced testbenches**.
