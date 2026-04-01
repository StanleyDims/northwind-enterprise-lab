# Lessons Learned

## 1. Hardware presentation matters
A virtualization project can fail before the OS layer if storage or controller presentation is not correct.

## 2. A successful clone is not the same as a usable clone
Even when a VM boots, network identity issues can still prevent proper functionality.

## 3. Document from day one
Waiting until later makes it harder to remember exact issues, fixes, and reasoning.

## 4. Keep the first phase simple
Early success comes from stability, not from trying to over-engineer segmentation immediately.

## 5. Snapshots are operational safety nets
They are not just convenience features; they reduce risk and improve confidence during experimentation.

## 6. Infrastructure work is iterative
Several issues only became clear after testing, observation, and correction. This is normal and part of real engineering work.