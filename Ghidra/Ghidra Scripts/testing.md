from ghidra.app.emulator import EmulatorHelper

FUNC = 0xdeadbeef
BP = FUNC +0x68
BUF_ADDR = toAddr(0xCOFFEE)
BUFSZ = 1024

emu = EmulatorHelper(currentProgram)

def main(param_1, param_2, param_3)
	emu.writeMemory(BUF_ADDR, '\x00' * BUFSZ)
	emu.writeRegister("rsp", BUF_ADDR)
	 emu.writeRegister(emu.getPCRegister(), FUNC)
	 emu.write("rdi", param_1)
	 emu.write("rsi", param_2)
	 ...
	 emu.setBreakpoint(BP)

	while True:
		if monitor.isCancelled():
			break
		emu.run(monitor)
		addr = getExecutionAddress()
		if addr == BP:
			break
		else:
			raise RuntimeError("Failed Emu")
		
		data = emu.ReadMemory(BUF, BUFSZ)
		try:
			print(data)
		except
			pass
main(p1,p2,p3)
emu.dispose