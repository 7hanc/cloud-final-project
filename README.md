# Cloud-final-project 
### (Team project, two members)
## Motivation
在管理伺服器時，管理者必須要能夠動態掌握個別使用的情況，使得當伺服器出現不當使用情況時，管理者能夠即時通知使用者或終止服務。然而，在提供雲端服務給使用者時，基於安全性與隱私，是不允許管理者進入使用者租的VM查看，並檢查VM是否正常使用。此外，透過進入個別VM檢查內部訊息的方式，是沒有效率且不實際的作法。據信在美國有1/3的VM都處於無用狀態，因此，提供一個簡單且有效率的偵測方法，是非常迫切與實際的問題。

## Definition of Zombie VM
目標: 找出持續占用系統資源但並沒有執行任何具有實際作用的VM。

## Create 2~3 scenarios causing VM useless
嘗試創造2-3種情況，使得VM無法正常運作，藉此來模擬所定義Zombie VM的使用情況
> Scenario 1: Fork bomb: :(){ :|: & };:   
> Scenario 2: Use sysrq-trigger to test: echo “c” > /proc/sysrq-trigger   
> Scenario 3: Insert a simple kernel module causing kernel panic: Dereference the NULL pointer

## A simple method to detect useless VMs
透過以上的觀察(Using vSphere)，我們發現當一台VM符合我們先前的Zombie VM定義時，該VM傾向於佔據某項系統資源(CPU/Memory)，而鮮少使用另一項資源。而正常的使用情況卻較傾向於平均使用這兩項資源。透過上述簡單的觀察，我們發現了一些無法正常運作VM的特徵。我們的靈感正是來自於此，然而，VM的資源使用率與這些特徵仍然是雜亂的，並不容易簡單有效的幫助管理者判別無用到的VM。

## Cost function method
我們將在 hypersivor端能夠取得的VM的CPU與Memory使用率當作變數，建立一個Cost function來計算個別VM的cost，cost越高者傾向於被判別為不正常的VM。透過這個簡單的方法，我們便能夠將看似雜亂無章的數據化為容易比較的Cost數字。我們利用比值的方式來放大這一項特徵，若兩項資源的使用率差距越大，則比值越高；反之若越相近則比值越低。
> Cost function: <img src="https://i.imgur.com/lcKYf5T.png"  width=50% height=50% />

## Experiment result
Write a simple script to collect resource utilization and compute cost
Use five VMs to test (Normal: VM1-2, Useless: VM3-5)
We assume threshold=5 (Use cost of each VMs to determine threshold.)
<img src="https://i.imgur.com/Mo2X7aX.png" width=50% height=50%/><img src="https://i.imgur.com/Mo2X7aX.png" width=50% height=50%/>

## Pros or Cons
* Pros:
  * Easy to realize
  * Is a general way to detect useless VMs without inspect each VM directly
* Cons:
  * Hard to distinguish some special case
  * Consider few factors
  * Consider too many factor may hard to set up cost function.

## Future work
* Use more real case to test the cost function and set up threshold.
* Use some algorithm or statistics method to find good cost function.
  * Regression Analysis
* Use more VM information (ex:number of process,I/O…etc) as parameter to refine the cost function.	



