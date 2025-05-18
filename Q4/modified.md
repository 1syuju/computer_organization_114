cc

```
#include "mem/cache/replacement_policies/lfu_rp.hh"

#include <cassert>
#include <memory>

#include "base/types.hh"
#include "params/LFURP.hh"

LFURP::LFURP(const Params *p)
    : BaseReplacementPolicy(p)
{
}

void
LFURP::invalidate(const std::shared_ptr<ReplacementData>& replacement_data) const
{
    auto data = std::static_pointer_cast<LFUReplData>(replacement_data);
    data->refCount = 0;
    data->lastTouchTick = 0;
}

void
LFURP::touch(const std::shared_ptr<ReplacementData>& replacement_data) const
{
    auto data = std::static_pointer_cast<LFUReplData>(replacement_data);
    data->refCount++;
    data->lastTouchTick = curTick();  // Record access time
}

void
LFURP::reset(const std::shared_ptr<ReplacementData>& replacement_data) const
{
    auto data = std::static_pointer_cast<LFUReplData>(replacement_data);
    data->refCount = 1;
    data->lastTouchTick = curTick();
}

ReplaceableEntry*
LFURP::getVictim(const ReplacementCandidates& candidates) const
{
    assert(!candidates.empty());

    ReplaceableEntry* victim = nullptr;
    unsigned minFreq = UINT_MAX;
    Tick oldestTick = Tick(-1);  // max tick

    for (const auto& entry : candidates) {
        auto data = std::static_pointer_cast<LFUReplData>(entry->replacementData);

        if (data->refCount < minFreq) {
            // Lower frequency: update
            minFreq = data->refCount;
            oldestTick = data->lastTouchTick;
            victim = entry;
        } else if (data->refCount == minFreq) {
            // Same frequency: choose the oldest one
            if (data->lastTouchTick < oldestTick) {
                oldestTick = data->lastTouchTick;
                victim = entry;
            }
        }
    }

    return victim;
}

std::shared_ptr<ReplacementData>
LFURP::instantiateEntry()
{
    return std::make_shared<LFUReplData>();
}

LFURP*
LFURPParams::create()
{
    return new LFURP(this);
}

```

hh
```
#ifndef __MEM_CACHE_REPLACEMENT_POLICIES_LFU_RP_HH__
#define __MEM_CACHE_REPLACEMENT_POLICIES_LFU_RP_HH__

#include "mem/cache/replacement_policies/base.hh"
#include "base/types.hh" // For Tick

struct LFURPParams;

class LFURP : public BaseReplacementPolicy
{
  protected:
    /** LFU-specific implementation of replacement data. */
    struct LFUReplData : ReplacementData
    {
        /** Number of references to this entry since it was reset. */
        unsigned refCount;

        /** Last time the block was accessed (for tie-breaking). */
        Tick lastTouchTick;

        /** Constructor: initialize counters. */
        LFUReplData() : refCount(0), lastTouchTick(0) {}
    };

  public:
    /** Convenience typedef. */
    typedef LFURPParams Params;

    LFURP(const Params *p);
    ~LFURP() {}

    void invalidate(const std::shared_ptr<ReplacementData>& replacement_data)
        const override;

    void touch(const std::shared_ptr<ReplacementData>& replacement_data) const
        override;

    void reset(const std::shared_ptr<ReplacementData>& replacement_data) const
        override;

    ReplaceableEntry* getVictim(const ReplacementCandidates& candidates) const
        override;

    std::shared_ptr<ReplacementData> instantiateEntry() override;
};

#endif // __MEM_CACHE_REPLACEMENT_POLICIES_LFU_RP_HH__

```
