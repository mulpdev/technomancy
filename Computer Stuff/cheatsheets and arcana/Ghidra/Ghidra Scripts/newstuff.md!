```python
def find_possible_destinations_of_inst(inst):
    '''
    walk back a few instructions looking for refs to some data or func

    TODO: Fix assumptions
    - first ref may not be the destination
    - refs may not be data only, it might be a func
    '''
    prev_inst = inst
    for i in range(5):
        prev_inst = prev.getPrevious()
        for ref in prev_inst.referencesFrom(): # "references FROM instruction to something else
            dst_addr = ref.getToAddress()
            data = PL.getDataContaining(dst_addr)
            return data.getMinAddress()
    return None

def find_calls_to_dst_func(func, dstfunc):
    ret = []
    addr_set_view = func.getBody()
    inst = PL.getInstructionAt(addr_set_view.getMinAddress())

    while addr_set_view.contains(inst.getAddress()):
        mnem = inst.getMnemonicString()
        if 'call' = mnem.lower():
            dst_addr_set_view = dstfunc.getBody()
            for ref in inst.getReferencesFrom():
                to_addr = ref.getToAddress()
                if dst_addr_set_view.contains(to_addr):
                    ret.append(ref.getFromAddress())
        inst = inst.getNext()
    return ret

def find_instruction_in_func(func, target_mnemonic="call"):
    addr_set_view = func.getBody() # address range of function
    inst = PL.getInstructionAt(addr_set_view.getMinAddress())

    while addr_set_view.contains(inst.getAddress())
        mnemonic = inst.getMnemonicString()
        if mnemonic == target_mnemonic:
            return find_possible_destinations_of_instruction(inst)

# =================================================

def get_data_referenced_by_instruction(addr):
    inst = PL.getInstructionAt(addr)
    refs_from = inst.getReferencesFrom()
    ref_to_addr = refs_from[0].getToAddress()# hopefully only 1 ref
    return ref_to_addr

# =================================================

def get_element_from_array_or_struct(addr, index):
    myarray = PL.getDataAt(myarray)
    return myarray.getComponent(index) # DataDB or DataComponent???


# =================================================

'''
If an address has multiple data types (array of structs, struct[0], struct0.field0_0x0, ...), drill down to the DT you want
'''
def get_specific_datatype_from_multiple(addr, target_data_str = ''):
    # tmp will be a DataDB then DataComponent for rest of loop
    tmp = PL.getDataAt(addr) # uses getDataAt() not getDataContaining() to not change examined address

    while tmp is not None:
        dt_mnem = tmp.getDataType().getMnemonic(tmp.getDefaultSettings()) # getDefaultSettings works for DataDB and DataComponent
        if data_mnem.lower() == target_dt_str.lower():
            return (curr_data, i) # call getComponent(0) i times on same nested type to get same result
    return None

# ============================================================

get_datatype_refs_in_func(target_func, target_dt):
    ret = []
    from ghidra.app.plugin.core.navigation.locationreferences import ReferenceUtils # this is a plugin to be called
    from ghidra.util.datastruct import ListAccumulator
    accum = ListAccumulator()
    ReferenceUtils.findDataTypeReferences(accum, target_dt, currentProgram, True, getMonitor())

    addr_set_view = target_func.getBody()
    for ref in accum:
        inst_addr = ref.getLocationOfUse()
        if not addr_set_view.contains(inst_addr):
            continue
        func = FM.getFunctionContaining(inst_addr)
        if func and func.getName() == target_func.getName():
            ret.append(inst_addr)
    return ret

# ====================================================

def find_uses_as_parameter(target_addr):
    ret = []
    from ghidra.program.model.symbol import RefType
    for ref in getReferencesTo(target_addr):
        if ref.getReferenceType() != RefType.PARAM:
            continue
        ret.append(ref)
    return ret

# ====================================================

def find_data_types:
    case_sensitive = False
    outlist = []
    #DTM.findDataTypes("DT_NAME_HERE", outlist) # no globbing
    DTM.findDataTypes("DT*_HER?", outlist, case_sensitive, getMonitor()) # globbing in name

    dt_names = []
    for dt in sorted(outlist):
        name = dt.getName()
        dtnames.append(name)

# ====================================================

def get_symbol_of_pointer_member():
    out = []
    data = PL.getDataAt(data_addr)
    for i in range(data.getNumComponents):
        member = data.getComponent(i)
        addr = member.getValue() # Will be address, not scalar
        symbol = SM.getPrimarySymbol(addr)
        symname = symbol.getName()
        out.append(addr, symname)
```
