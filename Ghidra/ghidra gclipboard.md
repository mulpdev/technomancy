```python
from docking.dnd import GClipboard
GClipboard.getSystemClipboard()

>>> GClipboard.getSystemClipboard().getContents(found[0]).getTransferDataFlavors()

>>> GClipboard.getSystemClipboard().getContents(found[0]).getTransferDataFlavors()[0]

>>> q = GClipboard.getSystemClipboard().getContents(found[0]).getTransferDataFlavors()[0]

>>> GClipboard.getSystemClipboard().getContents(found[0]).getTransferData(q)
 
```