    Title: 游戏中根据权重随机
    Date: 2015-01-06T00:00:00
    Tags: SWF


游戏策划经常会有需求，根据权重，从一张表格中随机某一条item

    struct Item
    {
        int id;
        int weight;
    }

使用上面的代码表示一条item

    std::list<Item> table;
    
使用上面的一行代码，表示一张表格

    // @return 0.0~1.0
    double rand(); 
    
假设有上面的随机函数
首先计算表格中所有item的权重总和

    int total=0;
    for(std::list<Item>::iterator iter=table.begin();
        iter != table.end(); ++iter)
    {
        total += iter->weight;
    }
    
接下来随机一个数字，在[0~total]之间

    int weight = rand()*total;
    
接下来根据weight从table中找到一条item

    total = 0
    for(std::list<Item>::iterator iter=table.begin();
        iter != table.end(); ++iter)
    {
        total += iter->weight;
        if(weight <= total)
        {
            return *iter;
        }
    }

就这样完成了策划的需求，根据权重，从一张表格中随机出了一条item
