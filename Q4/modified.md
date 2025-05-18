cc

```
#include "mem/cache/replacement_policies/lfu_rp.hh"

#include <cassert>
#include <memory>

#include "params/LFURP.hh"

LFURP::LFURP(const Params *p)
    : BaseReplacementPolicy(p)
{
}
//Resets both refCount and lastTouchTick to 0.
void
LFURP::invalidate(const std::shared_ptr<ReplacementData>& replacement_data)
const
{
    // Reset reference count
    std::static_pointer_cast<LFUReplData>(replacement_data)->refCount = 0;
    std::static_pointer_cast<LFUReplData>(
        replacement_data)->lastTouchTick = Tick(0);
}
//Increments refCount and updates lastTouchTick to the current tick (curTick()).
void
LFURP::touch(const std::shared_ptr<ReplacementData>& replacement_data) const
{
    // Update reference count
    std::static_pointer_cast<LFUReplData>(replacement_data)->refCount++;
    // Update last touch timestamp
    std::static_pointer_cast<LFUReplData>(
        replacement_data)->lastTouchTick = curTick();
}
//Resets refCount to 1 and updates lastTouchTick to the current tick (curTick()).
void
LFURP::reset(const std::shared_ptr<ReplacementData>& replacement_data) const
{
    // Reset reference count
    std::static_pointer_cast<LFUReplData>(replacement_data)->refCount = 1;
    std::static_pointer_cast<LFUReplData>(
        replacement_data)->lastTouchTick = curTick();
}
//If multiple candidates have the same refCount, it selects the one with the smallest lastTouchTick (i.e., the least recently accessed).
ReplaceableEntry*
LFURP::getVictim(const ReplacementCandidates& candidates) const
{
    // There must be at least one replacement candidate
    assert(candidates.size() > 0);

    // Visit all candidates to find victim
    ReplaceableEntry* victim = candidates[0];
    for (const auto& candidate : candidates) {
        // Update victim entry if necessary
        if (std::static_pointer_cast<LFUReplData>(
                    candidate->replacementData)->refCount <
                std::static_pointer_cast<LFUReplData>(
                    victim->replacementData)->refCount) {
            victim = candidate;
        }
        else if(std::static_pointer_cast<LFUReplData>(
                    candidate->replacementData)->refCount ==
                std::static_pointer_cast<LFUReplData>(
                    victim->replacementData)->refCount){
            if(std::static_pointer_cast<LFUReplData>(
                    candidate->replacementData)->lastTouchTick <
                std::static_pointer_cast<LFUReplData>(
                    victim->replacementData)->lastTouchTick){
                victim = candidate;
            }
        }
    }

    return victim;
}

std::shared_ptr<ReplacementData>
LFURP::instantiateEntry()
{
    return std::shared_ptr<ReplacementData>(new LFUReplData());
}

LFURP*
LFURPParams::create()
{
    return new LFURP(this);
}

```

hh
```
/** LFU-specific implementation of replacement data. */
struct LFUReplData : ReplacementData
    {
        /** Number of references to this entry since it was reset. */
        unsigned refCount;
        Tick lastTouchTick;//add this line
        //records the last time the cache entry was accessed

        /**
         * Default constructor. Invalidate data.
         */
        LFUReplData() : refCount(0), lastTouchTick(0) {}//add this line
    };

```
