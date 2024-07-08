https://reverseengineering.stackexchange.com/questions/30567/ghidrahow-to-set-global-variable-type

First get the `DataType` that you want, for example `struct foo`:

`DataType dt = getDataTypes("foo")[0];`

Or if it's just a pointer you'll have to get the pointer of that type.

You said you already have the address, you'll need to make sure it's an `Address` if not already:

`Address addr = toAddr(0x12345678);`

Then create the data:

`Data data = createData(addr, dt);`

It may already have something there if that fails, you can clear out that memory (there is another API for this if you need more control, this is the simple case):

`clearListing(addr, addr.add(dt.getLength() - 1);`